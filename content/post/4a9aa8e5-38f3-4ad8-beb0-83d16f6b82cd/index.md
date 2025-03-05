---
date: 2018-03-05
publish: true
slug: 5fe4f133-6a82-4e37-9b0a-f2a38891cb3d
tags:
- CS/CAndCpp
title: CppVectorInternal.md
---
### 基本概念

動態請求的array
就是太小重要更大的，然後搬過去
看code

```c++=
tem* resize(unsigned long vsize){
        tem *pay=new tem[vsize],*ppa,*endp=pay+vsize,*par;
        for(par=arr,ppa=pay;ppa<=endp&&par<=endc;++ppa,++par){
            *ppa=*par;
        }
        delete [] arr;
        arr=pay;crr=par;endc=endp;
        return arr;
    }
```

他有規範要寫的函式

有[標準](https://www.iso.org/standard/38110.html)IEEE死要錢:shit:

去看cpp reference
![](../da955d32-5bba-49bd-9043-c49e25843d37.jpg)
![](../7821f531-7444-4d18-8fb6-4e00c138dc10.jpg)

我有寫，示範:

> 帶大家去幹[apache](http://svn.apache.org/repos/asf/stdcxx/branches/4.2.x/include/vector)的(gun看不懂)



先換名子

```c++=
    typedef _TypeT                                     value_type;
    typedef _Allocator                                 allocator_type;
    typedef _TYPENAME allocator_type::size_type        size_type;
    typedef _TYPENAME allocator_type::difference_type  difference_type;
    typedef _TYPENAME allocator_type::reference        reference;
    typedef _TYPENAME allocator_type::const_reference  const_reference;
    typedef _TYPENAME allocator_type::pointer          pointer;
    typedef _TYPENAME allocator_type::const_pointer    const_pointer;
```

還有一些很噁爛的沒過來

看結構

```c++=
    iterator       _C_begin;
    iterator       _C_end;
    unsigned int * _C_bufend;
    
    struct _C_VectorAlloc: allocator_type {

        _C_VectorAlloc (const allocator_type &__alloc)
            : allocator_type (__alloc), _C_begin (), _C_end (), _C_bufend ()
            { /* empty */}

        pointer _C_begin;
        pointer _C_end;
        pointer _C_bufend;
    } _C_alloc;

```

直接指過去

建構子

```c++=
template<class _InputIter>
    vector  (_InputIter __first, _InputIter __last)
        : allocator_type (), _C_bufend ()
    {
      size_type __n = _DISTANCE (__first, __last, size_type);
      _C_init(__n); 
      _C_copy(__first, __last, _C_begin);
    }

```

假裝分配器已知，直接初始化
用前後家長度幹__n(vec.size())，應為那還是沒做除法XDDD

insert()

```c++=
template <class _InputIter>
    void _C_insert (const iterator &__it,
                   _InputIter __first, _InputIter __last, void*) {
        _RWSTD_ASSERT_RANGE (begin (), __it);
        _RWSTD_ASSERT_RANGE (__first, __last);

        // dispatch to an insert suitable for the category of InputIter
        _RWSTD_INSERT_RANGE (__it, __first, __last,
                             _RWSTD_ITERATOR_CATEGORY (_InputIter, __first));
    }
```

push_back()

```c++=
 void _C_push_back (const_reference __x) {
        _RWSTD_ASSERT (_C_alloc._C_end != _C_alloc._C_bufend);
        _C_alloc.construct (_C_alloc._C_end, __x);
        ++_C_alloc._C_end;
    }
```

pop_back()

```c++=
 void pop_back () {
        _RWSTD_ASSERT (!empty ());
        _C_alloc.destroy (_C_alloc._C_end - 1);
        --_C_alloc._C_end;
    }
```

```c++=
 bool empty () const {
        return _C_alloc._C_begin == _C_alloc._C_end;
    }
```

swap()

```c++=
swap (vector &__other)
{
    if (get_allocator () == __other.get_allocator ()) {
        pointer __tmp             = _C_alloc._C_begin;
        _C_alloc._C_begin         = __other._C_alloc._C_begin;
        __other._C_alloc._C_begin = __tmp;
        __tmp                     = _C_alloc._C_end;
        _C_alloc._C_end           = __other._C_alloc._C_end;
        __other._C_alloc._C_end   = __tmp;
        __tmp                     = _C_alloc._C_bufend;
        _C_alloc._C_bufend        = __other._C_alloc._C_bufend;
        __other._C_alloc._C_bufend = __tmp;
    }
    else {
        // not exception-safe
        _C_unsafe_swap (__other);
    }
}
```

會寫出來就是他直接copy，一個一個

asign()

```c++=
 void _C_assign (_InputIter __first, _InputIter __last, void*) {
        _RWSTD_ASSERT_RANGE (__first, __last);

        // dispatch to an assign suitable for the category of InputIter
        _RWSTD_ASSIGN_RANGE (__first, __last,
                             _RWSTD_ITERATOR_CATEGORY (_InputIter, __first));
    }
```

resize()

```c++=
 void _C_fill (iterator __first, iterator __last, bool __val) {
        while (__first != __last) *__first++ = __val;
    }

    void _C_fill_n (iterator __first, size_type __n, bool __val) {
        while (__n-- > 0) *__first++ = __val;
    }

    template <class _Iterator>
    iterator _C_copy (_Iterator __first, _Iterator __last, iterator __res) {
        while (__first != __last)
            *__res++ = *__first++;
        return __res;
    }
```

其實已經有[大大](https://hadibrais.wordpress.com/2013/11/10/dissecting-the-c-stl-vector-part-1-introduction/)幹過，寫得比我好太多了，不過他是做m$的
