# Install shadowsocks on Ubuntu 18.04

# Install Golang

Begin by running the following command to add the golang backports ppa:

```
sudo add-apt-repository ppa:longsleep/golang-backports
```

if you don't have `add-apt-repository` on your local machine, plese use this command:

```
sudo apt-get install software-properties-common
```

Then run the following two commands to update your packages from the ppa and install Golang v1.12

```
sudo apt-get update
sudo apt-get install golang-1.12
```

Now we need to add the go binary to our path. I like to put these lines in my `~/.profile`

```
GOPATH="~/go"
PATH="$PATH:/usr/lib/go-1.12/bin:$GOPATH/bin"
```

## Install shadowsocks

Install shadowsocks by using go get feature

```
go get github.com/shadowsocks/shadowsocks-go/cmd/shadowsocks-server
```

## Prepare the config file

A sample config file for shadowsocks server looks like this:

```
{
  "server": "0.0.0.0",
  "port_password": {
    "8880": "testconnect",
    "8881": "testconnect",
    "8882": "testconnect",
    "8883": "testconnect",
    "8884": "testconnect",
    "8885": "testconnect",
    "8886": "testconnect",
    "8887": "testconnect",
    "8888": "testconnect",
    "8889": "testconnect",
    "8890": "testconnect"
  },
  "timeout": 1800,
  "method": "aes-256-cfb"
}
```

## Start the shadowsocks service

```
screen -R ss
shadowsocks-server -c shadowsocks-config.json

# CTRL+A CTRL+D to safely quit from screen
```
