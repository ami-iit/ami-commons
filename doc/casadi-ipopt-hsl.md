# How to install CasADi + IPOPT + HSL

This guide should help you to install CasADi with IPOPT and HSL support.

0. Install some dependencies `sudo apt-get install build-essential gfortran liblapack-dev libmetis-dev libopenblas-dev` 
1. `mkdir -p  ~/robot-code/CoinIpopt && cd ~/robot-code/CoinIpopt`
2. Get [`coinbrew`](https://github.com/coin-or/Ipopt#using-coinbrew)  
   1. `wget https://raw.githubusercontent.com/coin-or/coinbrew/master/coinbrew`
   2. `chmod u+x coinbrew`
3. Run `./coinbrew Ipopt` and follow the instruction to fetch IPOPT and all the dependencies (Today the latest version of IPOPT is `3.13.4`)
4. `mkdir install`
5. Build ipot `./coinbrew build Ipopt --prefix=install --test --no-prompt --verbosity=3` (have a :coffee:)
6. Obtain an archive with HSL source code from http://www.hsl.rl.ac.uk/ipopt/. (I'm currently using `coinhsl-2019.05.21`)
7. Unzip the folder `coinhsl-2019.05.21.zip` into `~/robot-code/CoinIpopt/ThirdParty/HSL/` and rename it `coinhsl`.
9. `cd ~/robot-code/CoinIpopt` 
10. Build ipopt `./coinbrew build Ipopt --prefix=install --test --no-prompt --verbosity=3` (have a :coffee:)
11. Run `./coinbrew install Ipopt --no-prompt`
12. cd `~/robot-code/CoinIpopt/install/lib`
13. `ln -s libcoinhsl.so libhsl.so`
14. Add to the `.bashrc` the following lines
    ```sh
    export IPOPT_DIR=~/robot-code/CoinIpopt/install
    export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:${IPOPT_DIR}/lib/pkgconfig
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${IPOPT_DIR}/lib

    # ipopt this is required for casadi
    export PATH=${PATH}:${IPOPT_DIR}/lib
    
    # this may speed up ipopt 
    export OMP_NUM_THREADS=1
    ```
15. Clone CasADi `cd  ~/robot-code && git clone https://github.com/casadi/casadi.git`
16. `mkdir -p casadi/build && cd casadi/build`
17. Run `cmake  -DWITH_IPOPT:BOOL=ON -DWITH_HSL:BOOL=ON -DINCLUDE_PREFIX:PATH=include -DCMAKE_PREFIX:PATH=lib/cmake/casadi -DLIB_PREFIX:PATH=lib -DBIN_PREFIX:PATH=bin ..`
18. `make` and `make install`
19. Enjoy CasADi with IPOPT and HSL solvers :rocket:

