tokumx-osx-build
================

TokuMX osx binary package


Tokumx
-------------------

TokuMX is a high-performance, concurrent, compressing, drop-in replacement engine for MongoDB.


How to build
-------------------


    $ mkdir tokumx-build-osx
    $ cd tokumx-build-osx

cmake upgrade to 2.8.12+

    $ brew upgrade cmake
    $ git clone https://github.com/Tokutek/mongo
    $ git clone https://github.com/Tokutek/ft-index
    $ git clone https://github.com/Tokutek/jemalloc
    $ git clone https://github.com/Tokutek/backup-community
    
checkout to release tag, assume it's tokumx-1.5.0

    $ (cd mongo; git checkout tokumx-1.5.0)
    $ (cd ft-index; git checkout tokumx-1.5.0)
    $ (cd jemalloc; git checkout tokumx-1.5.0)
    $ (cd backup-community; git checkout tokumx-1.5.0)

symbol link dependency

    $ ln -snf ../../jemalloc ft-index/third_party/jemalloc
    $ cd mongo
    $ ln -snf ../../../ft-index src/third_party/ft-index
    $ ln -snf ../../../backup-community/backup src/third_party/backup
    $ mkdir build

generate cmake build script

    $ cd build
    $ cmake -D CMAKE_BUILD_TYPE=Release -D TOKU_DEBUG_PARANOID=OFF -D USE_VALGRIND=OFF -D USE_BDB=OFF -D BUILD_TESTING=OFF -D TOKUMX_DISTNAME=1.4.0 -D USE_CTAGS=OFF ..

manual patch or edit CMAKE_CXX_FLAGS,  add '-std=c++11 -stdlib=libc++' (not necessary for 1.5.0)

    $ cd ..
    $ vim CMakeLists.txt

    #add the follow line, 47 line
    set(CMAKE_CXX_FLAGS "-std=c++11 -stdlib=libc++ -fPIC -fno-strict-aliasing -ggdb")

For Mac OSX 10.9 and above (In Mac OSX 10.9, any reference to namespace tr1 is not necessary):
remove all occurences of "tr1::" in file:

    $vim mongo/src/mongo/platform/unordered_map.h
    $vim src/mongo/platform/unordered_set.h

compile and build

    $ cd build
    $ make -j4 package

if error shows there is function not used in mongo/src/third_party/ft-index/ft/block_table.cc,  comment these functions and make again:

    $ vim mongo/src/third_party/ft-index/ft/block_table.cc


