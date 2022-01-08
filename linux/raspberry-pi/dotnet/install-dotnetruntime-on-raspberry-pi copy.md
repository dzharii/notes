
# 2022 Install NET 6 SDK on raspberry pi

- [Download .NET 6.0  Linux, macOS, and Windows ](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)

```bash
sudo apt-get update
curl -sSL -o dotnet.tar.gz https://download.visualstudio.microsoft.com/download/pr/72888385-910d-4ef3-bae2-c08c28e42af0/59be90572fdcc10766f1baf5ac39529a/dotnet-sdk-6.0.101-linux-arm.tar.gz

sudo mkdir -p /opt/dotnet && sudo tar zxf dotnet.tar.gz -C /opt/dotnet
/opt/dotnet/dotnet --help

export DOTNET_ROOT=/opt/dotnet
export PATH=$PATH:/opt/dotnet

if ! grep -qF "/opt/dotnet" ~/.bashrc; then
  echo "export DOTNET_ROOT=/opt/dotnet" | tee -a ~/.bashrc
  echo "export PATH=\$PATH:/opt/dotnet" | tee -a ~/.bashrc
fi

```
