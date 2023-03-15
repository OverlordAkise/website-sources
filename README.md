# website-sources

A repository to manage my websites.

Please look out for license files!  
I only wrote the content of my 'luctus.at' and 'luctus.at/book' website and not the design itself. The design is made by "html5up.net | @ajlkn" with a "CC BY 3.0" license.  
Everything else, if not explicitly specified, is licensed as "GNU GPLv3".

Current websites:  
 - [https://shira.at](https://shira.at)  
 - [https://luctus.at](https://luctus.at)  
 - [https://luctus.at/book](https://luctus.at/book)
 - [https://luctus.at/wiki](https://luctus.at/wiki)


## Why upload your website?

I created an automated process that fetches git pushes and updates the websites accordingly. No more manual file copying from now on.


## Generate zip files for luctus.at

To generate the downloadable addons in the files/ folder of luctus.at I use the following bash line:

    find . -type d -maxdepth 1 -exec zip -r9 '../zips/{}.zip' '{}' \;

I simply let this run in a fixed interval to always keep the website up to date, hence why I didn't commit the zip files.
