[albertohm/graphite.md](https://gist.github.com/albertohm/5697429)

```sh

echo "Install python-pip"
sudo apt-get update
sudo apt-get install python-pip libcairo2 python-cairo -y

echo "Install Graphite and Dependencies"
sudo pip install carbon
sudo pip install whisper
sudo pip install django
sudo pip install django-tagging
sudo pip install graphite-web

```