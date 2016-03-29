Fix forward declaration of basic_string and list, for dual abi libstdc++ with inline std::__cxx11 namespace.

https://github.com/clearlinux/folly/commit/ca2e9c7f1b6bf92f1f67ae627692547710932577

Projects/f/folly/Traits.h line 19 add
  
``#include <list>``
``#include <string>``


@@ -239,19 +242,10 @@ FOLLY_NAMESPACE_STD_BEGIN
comment template define basic_string list

242 ``-#ifndef _GLIBCXX_USE_FB``
243 ``-template <class T, class R, class A>``
244 ``-  class basic_string;``
245 ``-#else``
246 ``-template <class T, class R, class A, class S>``
247 ``-  class basic_string;``
248 ``-#endif``
249 ``template <class T, class A>``
250     ``class vector;``
251  ``template <class T, class A>``
252    ``class deque;``
253 ``-template <class T, class A>``
254 ``-  class list;``
255  ``template <class T, class C, class A>``
256    ``class set;``
257  ``template <class K, class V, class C, class A>``
