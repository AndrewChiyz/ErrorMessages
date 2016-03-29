# Error messages in processing of fblualib installation on OS:Ubuntu 16.04 with GCC version 5.3.1, and libboost-1.58

In file install_all.sh, codes will apt-get a lot of dependencies, as well as dependencies of libiberty-dev when using OS version higher than 13.10. Thus, ***I first close the checking-system-version codes from line 25 to 37. and set the ``extra_packages=libiberty-dev`` mannually***. Then excute the installation code. Then it will git clone another three dependent packages including folly, fbthrift, thpp. And it seems that these three packages will be compiled and installed respectively.

***Error message: Fix forward declaration of basic_string and list, for dual abi libstdc++ with inline std::__cxx11 namespace.***

***Solution: <u>https://github.com/clearlinux/folly/commit/ca2e9c7f1b6bf92f1f67ae627692547710932577</u>***

Projects/fblualib-build/folly/folly/Traits.h line 19 add
  
``#include <list>``    
``#include <string>``    


@@ -239,19 +242,10 @@ FOLLY_NAMESPACE_STD_BEGIN
comment template define basic_string list

242 ``//#ifndef _GLIBCXX_USE_FB``    
243 ``//template <class T, class R, class A>``   
244 ``//  class basic_string;``   
245 ``//#else``    
246 ``//template <class T, class R, class A, class S>``    
247 ``//  class basic_string;``   
248 ``//#endif``   
249 ``template <class T, class A>``   
250     ``class vector;``    
251  ``template <class T, class A>``   
252    ``class deque;``     
253 ``//template <class T, class A>``   
254 ``//  class list;``    
255  ``template <class T, class C, class A>``   
256    ``class set;``   
257  ``template <class K, class V, class C, class A>``    

Then the compliation works fine, and Intallation is fine.

However, when compiling thrift, ***it cannot pass the check of libfolly.so***, which have already been installed successfully. In the ***config.log*** file of project thrift, and it shows the following error messages:

***undefined reference to make_fcontext jump_fcontext***

``/usr/local/lib/libfolly.so: undefined reference to `make_fcontext'``    
``/usr/local/lib/libfolly.so: undefined reference to `jump_fcontext'``    

***Solution: <u>https://github.com/facebook/folly/issues/205</u>.*** It seems that I didnot compile libfolly.so properly with libboost-1.58, thus I changed the ***folly/folly/m4/ax_boost_context.m4*** file in the folly projects as the solution described. and rebuild the libfolly.so, and install it.

@@ -70,7 +70,7 @@ AC_DEFUN([AX_BOOST_CONTEXT],

AC_COMPILE_IFELSE([AC_LANG_PROGRAM(   
     [[@%:@include <boost/context/all.hpp>]],   
//    [[boost::context::fcontext_t* fc = boost::context::make_fcontext(0, 0, 0);]])],    
     [[boost::context::fcontext_t fc = boost::context::make_fcontext(0, 0, 0);]])],     
     ax_cv_boost_context=yes, ax_cv_boost_context=no)   
     CXXFLAGS=$CXXFLAGS_SAVE    

Then, I passed the check of libfolly.so during compiling fbthrift. But, then I encountered new compile error messages :   

generate/t_rb_generator.cc: In member function ‘virtual void    t_rb_generator::generate_enum(t_enum*)’:    
generate/t_rb_generator.cc:315:11: error: operands to ?: have different types ‘bool’    and ‘std::basic_ostream<char>’   
     first ? first = false : f_types_ << ", ";    
generate/t_rb_generator.cc:326:11: error: operands to ?: have different types ‘bool’     and ‘std::basic_ostream<char>’   
     first ? first = false: f_types_ << ", ";    

***Solution comes from : <u>https://aur.archlinux.org/packages/fbthrift/</u>***
I think it might be that the new compiler GCC 5.3.1 is no longer support this type of syntax probably. So I changed the source code of t_rb_generator.cc as the above url described, which is:

``gokcen commented on 2015-10-11 21:17``     
``This fixes the issue:```     
``@@ -312,7 +312,11 @@ void t_rb_generator::generate_enum(t_enum* tenum) {``    
``//Populate the hash``     
``int32_t value = (*c_iter)->get_value();``    

``// first ? first = false : f_types_ << ", ";``    
``if (first)``    
``first = false;``    
``else``   
``f_types_ << ", ";``  
``f_types_ << value << " => \"" << capitalize((*c_iter)->get_name()) << "\"";``   
  
``}``   

``@@ -323,7 +327,11 @@ void t_rb_generator::generate_enum(t_enum* tenum) {``   
``first = true;``
``for (c_iter = constants.begin(); c_iter != constants.end(); ++c_iter) {``   
``// Populate the set``    
``// first ? first = false: f_types_ << ", ";``    
``if (first)``   
`` first = false;``   
``else``   
 ``f_types_ << ", ";``   
 
``f_types_ << capitalize((*c_iter)->get_name());``
``}``
``f_types_ << "]).freeze" << endl;``
 
Then, TH++ was installed properly. and fblualib also was installed successfully, after fixing a conflic of the new project path and previous generated project path in the build/CMakeCache.txt (I just removed the project from /tmp/ to /home/usrname/Projects/, thus I delete the build folder and make, re-generate new CMakecache.txt file, then everything goes fine now).



