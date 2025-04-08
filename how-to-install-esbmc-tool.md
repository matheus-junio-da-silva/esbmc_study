sudo apt update
sudo apt-get install -y clang-14 llvm-14 clang-tidy-14 python-is-python3 python3 git ccache unzip wget curl bison flex g++-multilib linux-libc-dev libboost-all-dev libz3-dev libclang-14-dev libclang-cpp-dev cmake
git clone https://github.com/esbmc/esbmc.git
cd esbmc
mkdir build && cd build

# Create a 4GB swap file

sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Check if it's working
free -h

cmake .. -DDOWNLOAD_DEPENDENCIES=ON -DENABLE_Z3=1 -DENABLE_SOLIDITY_FRONTEND=On -DBUILD_STATIC=OFF 
make -j$(nproc)

