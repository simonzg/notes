# Build Nakama

### Install jq

[website](https://stedolan.github.io/jq/download/)

Mac:

```
brew install jq
```

### Install Golang

Mac:

visit [website](https://golang.org/dl/) to download installer

Ubuntu: 

```
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt-get update
sudo apt-get install golang-go
```

### Install Nodejs

Mac:

visit [website](https://nodejs.org/en/download/) to download installer

Ubuntu: 

```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Install dep

[website](https://github.com/golang/dep)

Mac:

```
brew install dep
brew upgrade dep
```

Ubuntu:

```
go get -u github.com/golang/dep/cmd/dep
```

### Install protobuf (protoc)

Mac:

```
brew install protobuf
```

Ubuntu: 

```
sudo add-apt-repository ppa:maarten-fonville/protobuf
sudo apt-get update
sudo apt-get install protobuf
```

### Install make

Mac:

```
xcode-select --install
```

Ubuntu:

```
sudo apt-get install build-essential
```

### Install CockRoachDB

[website](https://www.cockroachlabs.com/docs/stable/install-cockroachdb.html)

Mac:

```
brew install cockroach
```

Ubuntu:

```
wget -qO- https://binaries.cockroachdb.com/cockroach-v1.0.6.linux-amd64.tgz \
| tar  xvz

cp -i cockroach-v1.0.6.linux-amd64/cockroach /usr/local/bin
```

### Prepare Code

> Assume you want to set /somewhere/go to be $GOPATH, this is shared across projects written in Go

**ADD THIS LINE to ~/.bashrc or ~/.zshrc depend on what shell you use**

```
export GOPATH=/somewhere/go
export PATH=$PATH:$GOPATH/bin
```


Then execute:

```
cd $GOPATH
mkdir src
cd src
git clone git@github.com/heroiclabs/nakama
```

### Build Nakama

```
make gettools
dep ensure
make dbstart nakama
./build/dev/nakama migrate up
./build/dev/nakama
```

### Reference

* [Build Instructions](https://github.com/heroiclabs/nakama)
* [Run nakama](https://heroiclabs.com/docs/install-docker-quickstart/)
