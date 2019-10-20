# Week 1
1. Install tools
    * [Webstorm](https://www.jetbrains.com/student/) or
    * [VSCode](https://code.visualstudio.com/) (or Atom, Brackets, etc...)
       * Install extensions for VSCode:
          * Press ctrl-shift-x or click extensions icon on the left panel.
          * Search and install:          
             * EditorConfig for VS Code
             * ESLint
             * JavaScript (ES6) Code Snippets
             * Prettier
    * Some file transfer app like WinSCP, FileZilla or Cyberduck
    * [Node.js](https://nodejs.org/en/) LTS version
       * MacOS no sudo for Mac users
         - [instructions](https://github.com/sindresorhus/guides/blob/master/npm-global-without-sudo.md)
    * [Git](https://git-scm.com/downloads) for Windows users
    * [Metropolia VPN](https://wiki.metropolia.fi/pages/viewpage.action?pageId=149652071)
    * [Install Educloud virtual computer](https://docs.google.com/document/d/10_NYlJdMaDE_Cv3yZvaZn2g9scs8-n7GOYxOgSrAgC0/edit#heading=h.vfts8ixd14uo)

1. GIT
   * Study [GIT material](https://github.com/mattpe/git-intro/blob/master/git-basics.md)
     * You don't have to do the exercises (yet)
   * [What files to include in repo?](git.md)
   * Common git commands:
      * Creating new local repo (first create remote repo in GitHub etc.)
          * git init
          * git remote add origin https://github.com/user/repo-name.git
          * git add . (this adds all files)
          * git commit -m 'some message'
          * git push -u origin master
   
      * Push new changes: 
         * git add .
         * git commit -m 'some message'
         * git push
      
      Remember to make README.md and especially .gitignore _before_ first commit
       
