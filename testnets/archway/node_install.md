
<details>
  <summary>
 <b>Prepare a server</b>
  </summary>
  
#### Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

#### Install Required Packages
```bash
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```

<details>
  <summary>
 Package Descriptions
  </summary>
      
>  - curl, wget: Tools for downloading files.  
> - clang, build-essential: Necessary for compiling software.  
> - pkg-config, libssl-dev, libleveldb-dev: Development libraries for software building.  
> - jq: Command-line JSON processor.  
> - git: Version control system.  
> - make, bsdmainutils: Build tools and utilities.  
> - ncdu, htop, screen: System monitoring and management utilities.  
> - unzip, bc: Tools for file decompression and arithmetic operations.  
> - fail2ban: Security utility to prevent brute force attacks.  

</details>

#### Install Go v1.21.6
```bash
ver="1.21.6" && \
cd $HOME && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

</details>

<details>
  <summary>
 <b>Node Install</b>
  </summary>
<details>
  <summary>
 <b>Installing Node Binary Files</b>
  </summary>

  <details>
  <summary>
 <b>Option 1: Building from Source</b>
  </summary>
    
```bash
git clone https://github.com/archway-network/archway.git archway && \
cd archway && \
git checkout v0.2.0 && \
make install
```
```bash
archwayd version --long | grep -e version -e commit
```
> version: 0.2.0   
> commit: 532f53724bf477c5c8826fae376906526a09ed2d   


</details>


  <details>
  <summary>
 <b>Option 2: Downloading the Precompiled Binarye</b>
  </summary>
    
</details>


</details>
</details>
