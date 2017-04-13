# Package development and maintenance utility functions that I find useful

## makeImport
Function that scrapes an R file or directory of R files to create calls for namespace import. 

Instead of manually cataloging where each function is imported from this allows developer to write the R script with namespaces syntax (ie library::function) and then run the function to create the output needed for eith an roxygen header or directly to NAMESPACE

When you are building a package to submit to cran and you need to have namespace calls for any function that is being imported.

It is a pain (at least for me) to manually parse through the code looking for all the `*::*` and writing it in the roxygen header.

this function does that for you. 

you can write normally your script with the namespace calls and in the end run the function and you can paste the output into the header. 

The function is written to work on single files or whole dirs (like in a package R subdir).

The addin uses the active document in the RStudio editor console as the file argument.

```r
makeImport(file=list.files_github('yonicd/YSmisc','R'),print = T,format = 'oxygen')
 
https://raw.githubusercontent.com/yonicd/YSmisc/master/R/grepDir.R

https://raw.githubusercontent.com/yonicd/YSmisc/master/R/importAddin.R
#' @importFrom rstudioapi getActiveDocumentContext
 
https://raw.githubusercontent.com/yonicd/YSmisc/master/R/listFilesGithub.R
#' @importFrom rvest html_text html_nodes
#' @importFrom xml2 read_html
 
https://raw.githubusercontent.com/yonicd/YSmisc/master/R/makeImport.R
#' @importFrom stringr str_extract_all
#' @importFrom utils installed.packages
 
https://raw.githubusercontent.com/yonicd/YSmisc/master/R/runStanGit.R
#' @importFrom RCurl getURL
 
https://raw.githubusercontent.com/yonicd/YSmisc/master/R/setwdURL.R
#' @importFrom stringr str_extract
```

```r
makeImport(list.files_github('yonicd/YSmisc','R'),print = T,format = 'namespace')

importFrom(RCurl,getURL)
importFrom(rstudioapi,getActiveDocumentContext)
importFrom(rvest,html_nodes)
importFrom(rvest,html_text)
importFrom(stringr,str_extract)
importFrom(stringr,str_extract_all)
importFrom(utils,installed.packages)
importFrom(xml2,read_html)
```

```r
makeImport(list.files_github('yonicd/YSmisc','R'),print = T,format = 'description')

Imports: rstudioapi,rvest,xml2,stringr,utils,RCurl
```

## grepDir
Function that for recursive grep in R to search in entire directory tree, returns grep by file that matches pattern. 

```r
#search computer
  grepDir(pattern = 'gsub',path = '.',value=TRUE,recursive = T)

$`./R/makeImport.R`
[1] "  names(s1)=gsub('\\\\\\\\b','',names(s1))"                                                           
[2] "      y=gsub('[\\\\\",(]','',unlist(stringr::str_extract_all(x,pattern=paste0(nm,'(.*?)\\\\('))))"    
[3] "    out=gsub('\\\\$.*','',out)"                                                                       
[4] "      ret=paste0(\"#' @importFrom \",gsub('::',' ',nm),gsub(nm,'',paste(unique(out),collapse = ' ')))"
[5] "        if(length(out)>=cut) ret=paste0(\"#' @import \",gsub('::','',nm))"                            
[6] "    pkgN=gsub(':.*','',pkg)"                                                                          
[7] "    ret=paste0('importFrom(',gsub('::',',',pkg),')')"                                                 
[8] "          sprintf('import(%s)',unique(gsub(':.*','',x))) "                                            
[9] "          paste0('importFrom(',gsub('::',',',grep(x,pkg,value=T)),')')"                               
[10] "    ret=unique(gsub('::(.*?)$','',unlist(pkg)))"                                                      

$`./R/runStanGit.R`
[1] "    #strsplit(gsub('\\\\r','',RCurl::getURL(code.loc)[1]),'\\\\n')[[1]]"                                                                            
[2] "  keep.files=gsub(' ','',unlist(lapply(strsplit(r.code[which(grepl('stan\\\\(',r.code))],'<-'),'[',1)))"                                            
[3] "  r.code=gsub('print','#print',r.code)"                                                                                                             
[4] "  r.code=gsub('pairs','#pairs',r.code)  "                                                                                                           
[5] "      r.code[stan.find[i]]=gsub(substr(r.code[stan.find[i]],x[1],x[2]),strip.path(substr(r.code[stan.find[i]],x[1]+1,x[2]-1)),r.code[stan.find[i]])"

$`./R/setwdURL.R`
[1] "  urlPath=gsub(r.file,'',path)"                         
[2] "  url.loc=gsub('master(.*?)$','',path)"                 
[3] "  dat.loc=gsub(paste0(url.loc,'master/'),'',urlPath)"   
[4] "  r.script=gsub('\\\\s+','',r.script)"                  
[5] "  str.change=basename(gsub('[\\\\\"]','',str.old))"     
[6] "      file.name=gsub(' ','',strsplit(x,'<-|=')[[1]][1])"
[7] "      gsub(str.old,file.name,x) 

#search github
  grepDir(pattern = 'importFrom',path = c('yonicd/YSmisc','R'),value=TRUE)
$`R/grepDir.R`
[1] "#' grepDir(pattern = 'importFrom',path = c(repo='yonicd/YSmisc',subdir='R'),value=TRUE)"

$`R/importAddin.R`
[1] "#' @importFrom rstudioapi getActiveDocumentContext"

$`R/ls_github.R`
[1] "#' @importFrom httr content GET"

$`R/makeImport.R`
[1] "#' @param cut integer number of functions to write as importFrom until switches to import"            
[2] "#' @importFrom stringr str_extract_all"                                                               
[3] "#' @importFrom utils installed.packages"                                                              
[4] "      ret=paste0(\"#' @importFrom \",gsub('::',' ',nm),gsub(nm,'',paste(unique(out),collapse = ' ')))"
[5] "    ret=paste0('importFrom(',gsub('::',',',pkg),')')"                                                 
[6] "          paste0('importFrom(',gsub('::',',',grep(x,pkg,value=T)),')')"                               

$`R/runStanGit.R`
[1] "#' @importFrom RCurl getURL"
```

## ls_github
Return raw paths to files in github directory. 

```r
#head of master branch
ls_github(repo='yonicd/YSmisc')
[1] "https://raw.githubusercontent.com/yonicd/YSmisc/master/DESCRIPTION"
[2] "https://raw.githubusercontent.com/yonicd/YSmisc/master/NAMESPACE"  
[3] "https://raw.githubusercontent.com/yonicd/YSmisc/master/README.md"  
[4] "https://raw.githubusercontent.com/yonicd/YSmisc/master/misc.Rproj" 

#subdirectory R
ls_github(repo='yonicd/YSmisc',subdir='R')
[1] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/grepDir.R"        
[2] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/importAddin.R"    
[3] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/listFilesGithub.R"
[4] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/makeImport.R"     
[5] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/runStanGit.R"     
[6] "https://raw.githubusercontent.com/yonicd/YSmisc/master/R/setwdURL.R"

#recursive call
ls_github(repo='yonicd/YSmisc',subdir='inst',recursive = FALSE) #returns nothing
> character(0)
ls_github(repo='yonicd/YSmisc',subdir='inst',recursive = TRUE)
[1] "https://raw.githubusercontent.com/yonicd/YSmisc/master/inst/rstudio/addins.dcf"
```