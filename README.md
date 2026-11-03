# Boost 1.81.0 on loongarch64

## 必须排除 context / coroutine / fiber

Boost 1.81 的 `boost::context` **没有 loongarch64 汇编实现**（上游 1.83+ 才加），直接编会报：

    error: No best alternative for libs/context/build/asm_sources

已确认 Doris BE 只用 `lexical_cast / tokenizer / stacktrace / dynamic_bitset / regex / noncopyable / shared_ptr / operators`，
**不用** context / coroutine / fiber（brpc 有自己的 bthread），排除是安全的。

    ./bootstrap.sh --prefix=$PREFIX --with-toolset=gcc
    ./b2 -q link=static runtime-link=static -j4 \
        --without-mpi --without-graph --without-graph_parallel --without-python \
        --without-context --without-coroutine --without-fiber \
        cxxflags="-std=c++17 -g" install

结果：16801 targets 全部编译通过，0 error，39 个静态库 + 头文件。

## 下载

`archives.boost.io` 只有 25KB/s（140MB 要 1.5 小时），`downloads.sourceforge.net/project/boost/boost/1.81.0/boost_1_81_0.tar.gz` 有 2.4MB/s。
