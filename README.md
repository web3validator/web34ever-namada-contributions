# web34ever-namada-contributions
I have created "Namada in Motion," a 3D logo reveal that captures the essence of Namada's dynamic and innovative spirit. This animation isn't just a visual treat; it's a symbol of our evolving identity and commitment to progress in the blockchain space.

https://github.com/web3validator/web34ever-namada-contributions/assets/59205554/16c61ad2-0839-4480-80cf-f6d1d9922945



# Namada Chain Upgrade Guide for Validators

With the frequent upgrades and changes in the Namada chain, it's crucial for validators to stay updated with the latest versions. I've prepared a comprehensive guide to help you seamlessly upgrade your Namada setup, whether it's for a chain switch or a regular binary update.

For Chain Switch Upgrade:
Use these commands to update when there's a new chain version.

# Set new version and chain ID
```bash
NEWTAG=v0.23.1
NEWCHAINID=public-testnet-14.5d79b6958580
path_namada=$HOME
```
# Backup and remove old data
```bash
rm -r namada_backup
cp -r $HOME/.local/share/namada/pre-genesis $HOME/namada_backup/
```
```bash
sudo systemctl stop namadad && sudo systemctl disable namadad
sudo rm /usr/local/bin/namada /usr/local/bin/namadac /usr/local/bin/namadan /usr/local/bin/namadaw /usr/local/bin/namadar -rf
sudo rm $HOME/.local/share/namada -rf
sudo rm -rf $HOME/.masp-params
sudo apt update && sudo apt upgrade -y
```
# Environment variables update
```bash
sed -i '/public-testnet/d' "$HOME/.bash_profile"
sed -i '/NAMADA_TAG/d' "$HOME/.bash_profile"
echo "export BASE_DIR=$HOME/.local/share/namada" >> ~/.bash_profile
echo "export NAMADA_TAG=$NEWTAG" >> ~/.bash_profile
echo "export CHAIN_ID=$NEWCHAINID" >> ~/.bash_profile
source ~/.bash_profile
```
# Build new release
```bash
cd $path_namada/namada
git fetch && git checkout $NAMADA_TAG
make build-release
```
# Copy new binaries and restart service
```bash
cd $HOME && sudo cp "$path_namada/namada/target/release/namada" /usr/local/bin/namada && \
sudo cp "$path_namada/namada/target/release/namadac" /usr/local/bin/namadac && \
sudo cp "$path_namada/namada/target/release/namadan" /usr/local/bin/namadan && \
sudo cp "$path_namada/namada/target/release/namadaw" /usr/local/bin/namadaw && \
sudo cp "$path_namada/namada/target/release/namadar" /usr/local/bin/namadar
sudo systemctl enable namadad
```
```bash
namada --version
VALIDATOR=web34ever
mkdir $HOME/.local/share/namada
cp -r $HOME/namada_backup $BASE_DIR/pre-genesis
namada client utils join-network --chain-id $CHAIN_ID --genesis-validator $VALIDATOR
sed -i 's/127.0.0.1:26657/127.0.0.1:27357/; s/127.0.0.1:26658/127.0.0.1:27358/ ; s/0.0.0.0:26656/0.0.0.0:27356/' $HOME/.local/share/namada/$NEWCHAINID/config.toml
sudo systemctl restart namadad && sudo journalctl -u namadad -f -o cat
```
For Regular Binary Update:
When the chain isn't changing, but a binary update is needed.

# Update to the new version
```bash
NEWTAG=v0.23.1
path_namada=$HOME
```
# Update environment variables
```bash
sed -i '/NAMADA_TAG/d' "$HOME/.bash_profile"
echo "export NAMADA_TAG=$NEWTAG" >> ~/.bash_profile
source ~/.bash_profile
```
# Build new release and update binaries
```bash
cd $path_namada/namada
git fetch && git checkout $NAMADA_TAG
make build-release
service namadad stop
cd $HOME && sudo cp "$path_namada/namada/target/release/namada" /usr/local/bin/namada && \
sudo cp "$path_namada/namada/target/release/namadac" /usr/local/bin/namadac && \
sudo cp "$path_namada/namada/target/release/namadan" /usr/local/bin/namadan && \
sudo cp "$path_namada/namada/target/release/namadaw" /usr/local/bin/namadaw && \
sudo cp "$path_namada/namada/target/release/namadar" /usr/local/bin/namadar
namada --version
service namadad start
```
These guides are designed to be straightforward and user-friendly for validators, ensuring your node stays up-to-date with minimal downtime. Your participation in these updates significantly contributes to the strength and stability of the Namada network.
