---
layout: post
title: "深入cocos2d-x中的touch事件"
date: 2013-08-27 12:11
comments: true
categories: Cocos2d-x
---
在文章[cocos2d-x中处理touch事件](http://www.cnblogs.com/xiaohan-wu/p/3187994.html "cocos2d-x中处理touch事件")中简单讨论过怎样处理touch事件, 那么今天来深入了解下cocos2d-x中是怎样分发touch事件的。

我们最先来看到CCTouchDispatcher这个类, 这个类在cocos2d-x中是管理和分发touch事件,  这个类继承于EGLTouchDelegate:

	class CC_DLL EGLTouchDelegate
	{
	public:
    	virtual void touchesBegan(CCSet* touches, CCEvent* pEvent) = 0;
    	virtual void touchesMoved(CCSet* touches, CCEvent* pEvent) = 0;
    	virtual void touchesEnded(CCSet* touches, CCEvent* pEvent) = 0;
    	virtual void touchesCancelled(CCSet* touches, CCEvent* pEvent) = 0;
    	virtual ~EGLTouchDelegate() {}
    };

那么这个类是触摸的开始, 当然这里指的只是cocos2d-x这个库的外层接口, 而不包括openGL的部分。

在CCTouchDispatcher类中有两个方法: addStandardDelegate 和 addTargetedDelegate, 相信大家都用过的, 对, 这就添加多点和单点触摸的方法, 这个类里面定义了两个数组来管理这些添加进来的delegate, 然后当touch事件传到这个类时, 也就是会调用这个类的touchesBegan等这系列方法。而这四个方法都只是调用了下面这个方法:

	void touches(CCSet *pTouches, CCEvent *pEvent, unsigned int uIndex);
	
而这个方法主要内容就是分发touch事件, 首先分发的是单点事件:

	//判断是否有单点的handler
	if (uTargetedHandlersCount > 0)
    {
        CCTouch *pTouch;
        CCSetIterator setIter;
        for (setIter = pTouches->begin(); setIter != pTouches->end(); ++setIter)
        {
            pTouch = (CCTouch *)(*setIter);

            CCTargetedTouchHandler *pHandler = NULL;
            CCObject* pObj = NULL;
            //遍历所有单点的handler, 开始分发不同的事件
            CCARRAY_FOREACH(m_pTargetedHandlers, pObj)
            {
                pHandler = (CCTargetedTouchHandler *)(pObj);

                if (! pHandler)
                {
                   break;
                }

                bool bClaimed = false;
                if (uIndex == CCTOUCHBEGAN) //touchBegan
                {
                	//这里拿到的就是我们经常在ccTouchBegan里面是返回的true or false
                    bClaimed = pHandler->getDelegate()->ccTouchBegan(pTouch, pEvent);

                    if (bClaimed)//如果为true, 说明这个delegate要处理事情
                    {
                        pHandler->getClaimedTouches()->addObject(pTouch);
                    }
                } else
                 if (pHandler->getClaimedTouches()->containsObject(pTouch))
                {
                    // moved ended canceled
                    bClaimed = true;

                    switch (sHelper.m_type)//分发事件的类型
                    {
                    case CCTOUCHMOVED:
                        pHandler->getDelegate()->ccTouchMoved(pTouch, pEvent);
                        break;
                    case CCTOUCHENDED:
                        pHandler->getDelegate()->ccTouchEnded(pTouch, pEvent);
                        pHandler->getClaimedTouches()->removeObject(pTouch);
                        break;
                    case CCTOUCHCANCELLED:
                        pHandler->getDelegate()->ccTouchCancelled(pTouch, pEvent);
                        pHandler->getClaimedTouches()->removeObject(pTouch);
                        break;
                    }
                }
					//如果你返回了true并且你的类swallow掉了touch事件, 那么下面的touch事件就不再继续了
                if (bClaimed && pHandler->isSwallowsTouches())
                {
                    if (bNeedsMutableSet)
                    {
                        pMutableTouches->removeObject(pTouch);
                    }

                    break;
                }
            }
        }
    }
    
这就是单点的事件了, 所以不论你把类的优先级怎么设置, 单点事件都是在多点事件之前处理的。而多点事件基本类似, 这里就不再赘述了。

然后我们再回到添加delegate的方法上, 来看看优先级的处理, addStandardDelegate 和 addTargetedDelegate都会调用下面这个添加方法:

	void CCTouchDispatcher::forceAddHandler(CCTouchHandler *pHandler, CCArray *pArray)
	{
    unsigned int u = 0;

    CCObject* pObj = NULL;
    CCARRAY_FOREACH(pArray, pObj)
     {
         CCTouchHandler *h = (CCTouchHandler *)pObj;
         if (h)
         {
         	 //查找比自己优先级数字大的并在它的位置插入
             if (h->getPriority() < pHandler->getPriority())
             {
                 ++u;
             }
 
             if (h->getDelegate() == pHandler->getDelegate())
             {
                 CCAssert(0, "");
                 return;
             }
         }
     }

    pArray->insertObject(pHandler, u);
    }
这个方法主要是按照delegate的优先级来添加delegate, 所以代码中就表明了优先级数字越小, 优先级就越高, 但是这只是针对同种类型的touch事件。

这个类的其他方法, 比如移除delegate等等就不在这里赘述了, 好了, 这篇就到这里了。 