# Contributing

Once you have gone through all the steps in the `README.md` start by creating a repo for your app eg.: [https://github.com/mpg-age-bioinformatics/shiny-histogram](https://github.com/mpg-age-bioinformatics/shiny-histogram) (you can check the link for a full running example). Don't forget to initiate your repo with a `README.md`.

Add this app as a submodule to the `shiny` repo:
```bash
cd ~/shiny
git submodule add git@github.com:mpg-age-bioinformatics/shiny-histogram histogram
```
Pull all the content of this app:
```bash
cd ~/shiny
git submodule init histogram
```
Update from the remote as often as you need with:
```bash
cd ~/shiny
git submodule update histogram
````
The following files and folders will be required:
```bash
cd ~/shiny/histogram
mkdir libs
touch requirements.R 
touch server.R
touch ui.R
```
The `requirements.R` file will have to include the installation of all required libraries.
Example of a `requirements.R` file:
```bash
.libPaths("/srv/shiny-server/histogram/libs")

if(!require(futile.logger)){
  install.packages("futile.logger", dependencies = TRUE)
  library(futile.logger)
}
  
if(!require(xlsx)){
  install.packages("xlsx", dependencies = TRUE)
  library(xlsx)
}
```
Please notice `.libPaths("/srv/shiny-server/histogram/libs")` pointing all libraries to be installed in the repos `libs` folder.

### Docs and version control

Always add a small documentation to your `ui.R` eg.:
```
    mainPanel(
      .
      .
      p("This App uses R's ", code('hist'), " function. For more information read the respective documentation in ",
        a("rdocumentation.org", href = "https://www.rdocumentation.org/packages/graphics/versions/3.5.1/topics/hist"),
        "and wikipedia's entry for ", a("histogram.",href="https://en.wikipedia.org/wiki/Histogram" )),
      p("Please keep the version tag on all downloaded files."),
      htmlOutput('appversion')
      )
```
Notice the `htmlOutput('appversion')` paragraph which makes use of the `gitversion()` function in `server.R`:
```
gitversion <- function(){ 
  git<-read.csv("/srv/shiny-server/.git/modules/histogram/refs/heads/master", header=FALSE)
  git<-git$V1
  git<-toString(git[1])
  git<-substr(git, 1, 7)
  return(git)
}
.
.
  output$appversion <- renderText ({ 
    paste0('App version: <b>',gitversion(),'</b>')
    }
  )
```
Please make sure that all files downloaded by the user containt the version tag eg.:
```
    # specify the output file name
    filename = function(){
      paste0('Histogram.',gitversion(),'.pdf')
    }
```

### Committing changes

You can push your changes to the repo's app by:
```bash
cd ~/shiny/histogram
git add .
git commit -m "<describe your changes here>"
git push origin HEAD:master
```
and then tell the main project to start tracking the updated version:
```bash
cd ~/shiny
git add histogram
git commit -m histogram
git push
```

### Development environment 

To help you with development we have deployed an RStudio server in the main container. 

After starting the container as descrived in the `README.md` you can start the RStudio server:
```bash
docker exec -i -t shiny sudo rstudio-server start
```
You can now access the RStudio server on you browser over [http://localhost:8787](http://localhost:8787) (user/password: mpiage/bioinf). This app can then be found in `/srv/shiny-server/histogram`. If you need to add R libraries make sure you do so in the `requirements.R` file and that those libraries are added to the `/srv/shiny-server/histogram/libs` folder. Therefore, `server.R`, `ui.R`,  and `requirements.R` should all start with `.libPaths("/srv/shiny-server/<AppName>/libs")`. If you need to add system libraries you can do so by entering the container with:
```bash
docker exec -i -t shiny /bin/bash
```
Make sure any installed libraries are added to the end of the [Dockerfile](https://github.com/mpg-age-bioinformatics/shiny/blob/master/Dockerfile) and that the respective changes are committed.

