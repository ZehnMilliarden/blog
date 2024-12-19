---
layout: default
title: COM编程基础
nav_order: 1
description: ""
permalink: /cs/prog/cpp/com
has_children: false
grand_parent: a46c73a3-f86b-4e8b-b199-9facf2d95e9d
parent: 4520f81a-f907-4e87-a475-da668552ad52
guid: a90d262b-f0d7-434e-9aa5-3ad78e01fc9d
---

# COM编程基础
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 一、IUnknown 接口
我们先简单看下IUnknown接口的定义：
```cpp
MIDL_INTERFACE("00000000-0000-0000-C000-000000000046")
IUnknown
{
    public:
    BEGIN_INTERFACE
    virtual HRESULT STDMETHODCALLTYPE QueryInterface( 
        /* [in] */ REFIID riid,
        /* [iid_is][out] */ _COM_Outptr_ void __RPC_FAR *__RPC_FAR *ppvObject) = 0;

    virtual ULONG STDMETHODCALLTYPE AddRef( void) = 0;

    virtual ULONG STDMETHODCALLTYPE Release( void) = 0;

    template<class Q>
    HRESULT
#ifdef _M_CEE_PURE
    __clrcall
#else
    STDMETHODCALLTYPE
#endif
    QueryInterface(_COM_Outptr_ Q** pp)
    {
        return QueryInterface(__uuidof(Q), (void **)pp);
    }

    END_INTERFACE
};
```
IUnknown接口包含了3个基础方法，分别是**QueryInterface**，**AddRef**，**Release**。  
- AddRef 和 Release 通过控制对象的引用计数来控制生命周期。  
- QueryInterface 是该对象其他接口的查询方法，参数分别是要查询接口的 IID 和 一个传出参数，以供查询者接收。  

例如：
```cpp
MIDL_INTERFACE("eabd8132-1ac2-4cf2-99aa-e94d1189ea2b")
InfComDemo : IUnknown
{
    virtual HRESULT STDMETHODCALLTYPE Method1() = 0;
    virtual HRESULT STDMETHODCALLTYPE Method2() = 0;
};

MIDL_INTERFACE("13fb16b2-25ff-4c83-a7fa-375107e00267")
InfComDemoEx : InfComDemo
{
    virtual HRESULT STDMETHODCALLTYPE Method3() = 0;
    virtual HRESULT STDMETHODCALLTYPE Method4() = 0;
};

CComPtr<InfComDemo> pInfComDemo;
CComPtr<InfComDemoEx> pInfComDemoEx;
loader->CreateInstance(pInfComDemo);

pInfComDemoEx->QueryInterface(&pInfComDemo);
CComPtr<InfComDemoEx> pInfComDemo3;
pInfComDemo3 = pInfComDemo;
```     
这里没有使用原始的接口指针来实现，而是使用了ATL标准库提供的模板类CComPtr对指针进行管理。      
通常来说，可以简单的通过QueryInterface来判断该对象对某个接口的支持程度是一个不错的办法。


## 二、COM对象的实现
我们基于上述的两个接口，先简单的实现一个类   
2.1 ClsComDemoImpl.h     
```cpp
class ClsComDemoImpl
    : public InfComDemoEx
    , public CComObjectRootEx<CComMultiThreadModel>
{
public:
    using ThisClass = ClsComDemoImpl;
    using ThisCoClass = CComObject<ThisClass>;
    using ThisCoAggClass = CComAggObject<ThisClass>;

public:
    ClsComDemoImpl();
    ~ClsComDemoImpl();

    BEGIN_COM_MAP(ClsComDemoImpl)
        COM_INTERFACE_ENTRY(InfComDemo)
        COM_INTERFACE_ENTRY(InfComDemoEx)
    END_COM_MAP()
    DECLARE_COM_MY_INSTANCE_CREATER(ThisClass)

public: //InfComDemo
    virtual HRESULT STDMETHODCALLTYPE Method1() override;
    virtual HRESULT STDMETHODCALLTYPE Method2() override;
public: //InfComDemoEx
    virtual HRESULT STDMETHODCALLTYPE Method3() override;
    virtual HRESULT STDMETHODCALLTYPE Method4() override;
};
```   
   
2.2 ClsComDemo.h
```cpp
class ATL_NO_VTABLE ClsComDemo
    : public ClsComDemoImpl
    , public CComCoClass<ClsComDemoImpl, &CLSID_ClsComDemo>
{
public:
    ClsComDemo() = default;
    ~ClsComDemo() = default;
    DECLARE_CLASSFACTORY()
    DECLARE_REGISTRY_RESOURCEID(IDR_CLSCOMDEMO)
    DECLARE_AGGREGATABLE(ThisClass)
    DECLARE_COM_MY_INSTANCE_CREATER(ThisClass)
};
OBJECT_ENTRY_AUTO(CLSID_ClsComDemo, ClsComDemo);
```

先看这两个文件


## 三、COM对象的创建
- 3.1 模块内的COM对象创建    
```cpp
CComPtr<CComObject<ClsComDemo>> pTarget;
CComObject<ClsComDemo>::CreateInstance(&pTarget);
```
