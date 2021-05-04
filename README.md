# CO-719


## RapiDoc
[RapiDoc Api](https://mrin9.github.io/RapiDoc/api.html)  
[RapiDoc Examples](https://github.com/mrin9/RapiDoc/tree/master/docs/examples)  
[RapiDoc Prototype HTML](https://github.com/LarsFischer97/CO-719/blob/main/api_rapidoc.html)  
[RapiDoc Zusammenfassung PDF](https://github.com/LarsFischer97/CO-719/blob/main/RapiDoc%20-%20Zusammenfassung.pdf)  

## Widdershins & Reslate  
 [Widdershins Options](https://github.com/Mermade/widdershins#options)  
 [Widdershins Reslate Prototype](https://github.com/LarsFischer97/CO-719/tree/main/_site)  

### Setup
1. Widdershins installation:  
    - Clone the [widdershins git repository](https://github.com/Mermade/widdershins), and npm i to install dependencies, or
    - npm install -g widdershins to install globally  

2. Reslate installation:  
    - Clone the [reslate git repository](https://github.com/Mermade/reslate), and npm i to install dependencies.  

3. Add json/md/yaml file into /defs in widdershins folder or navigate to it in "--summary" in the following command  

        node widdershins --search false --language_tabs 'ruby:Ruby' 'python:Python' --summary defs/YOURFILE.json/md/yaml -o YOURNEWFILE.md

4. Navigate into reslate folder and replace index.md in /source with YOURNEWFILE.md  
5. Enter following command in reslate folder  

        npm run build  
    


## ReDoc

[ReDoc Prototype HTML](https://github.com/LarsFischer97/CO-719/blob/main/api_redoc.html)


## Zusatz


[<img src="https://i.imgur.com/3hFPgPP.png">]()
