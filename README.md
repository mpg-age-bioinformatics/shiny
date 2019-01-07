# shiny
This repo hosts the backbone of our shiny apps

## Running the shiny server locally

You cloning this repo and building the required docker image:

```bash
cd ~/
mkdir -p shinylogs
git clone https://github.com/mpg-age-bioinformatics/shiny.git
cd shiny
docker build -t shiny .
```
Pull all our apps with:
```bash
git submodule update --init --recursive
```
and update the apps when required with:
```bash
git submodule update --recursive --remote
```
Run the container:
```bash
docker run --rm -p 3838:3838 -p 8787:8787 \
-v ~/shiny:/srv/shiny-server/ \
-v ~/shinylogs:/var/log/shiny-server/ \
--name shiny shiny
```
List the apps by:
```bash
cd ~/shiny
ls -d */
```
And now access the Apps by directing your browser to http://localhost:3838/AppName
(replacing AppName by the name of the respective App).

The container can be stopped and the container removed with:
```bash
docker stop shiny && docker rm shiny
``` 
The build image can be removed with:
```bash
docker rmi shiny
```