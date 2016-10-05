---
title: Simple Factory and Strategy Pattern
date: 2016-10-05 09:51:51
comment: true
tags: [design pattern, java]
---

<!-- more -->
------
## 前言
简单工厂模式和策略模式的学习

## 简单工厂模式
    //接口类，提供对外接口
    Interface Operation{
      String getResult(String var1, String var2);
    }
    //实现接口方法
    class Add{
      String getResult(String var1, String var2){
        return NumberUtils.createInteger(var1)+NumberUtils.createInteger(var2);
      }
    }
    //简单工厂，根据条件创建接口的实现类对象
    class OperationFactory{
      Operation createOptrion(String var){
        switch(var){
          case "1":{
            return new Add();
            break;
          }
        }
      }
    }
## 策略模式
    //策略的上下文
    class OptionContext{
      private IOperation iOperation;
      public OptionContext(IOperation iOperation){
        this.iOperation = iOperation;
      }
      String getResult(String var1, String var2){
        return iOperation.caculate(var1, var2);
      }
    }
    //接口抽象
    Interface IOperation{
      String caculate(String var1, String var2);
    }
    //具体实现
    class IntegerOption{
      String caculate(String var1, String var2){
        return NumberUtils.createInteger(var1)+NumberUtils.createInteger(var2);
      }
    }
    //使用
    OptionContext otx = new OptionContext(new IntegerOption);
    String res = otx.getResult(var1, var2);
## 简单工厂与策略的对比
  感整合悟：*简单工厂和策略模式都将变化封装在某个类中，工厂模式：客户端是使用工厂类生产的对象进行操作，策略模式：客户端使用自己
  传给Context的策略的对象进行操作。*
  **简单工厂规定一个模板，创建的对象符合这个模板，这是一种自上而下的设计方式;策略模式将相似的算法提炼到某个类中，封装统一的接口，是一种自下而上的设计方式。**
## 简单工厂和策略模式的整合
    //策略上下文
    class OptionContext{
      private IOperation iOperation;
      public OptionContext(String type){
        switch(type){
          case "add":{
            iOperation = new IntegerOption();
            return;
          }
        }
      }
      String getResult(String var1, String var2){
        return iOperation.caculate(var1, var2);
      }
    }
    //接口抽象
    Interface IOperation{
      String caculate(String var1, String var2);
    }
    //具体实现
    class IntegerOption{
      String caculate(String var1, String var2){
        return NumberUtils.createInteger(var1)+NumberUtils.createInteger(var2);
      }
    }
