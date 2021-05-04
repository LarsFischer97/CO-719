# CO-719
[CO-719 Forschung API Doku APP](https://confluence.d-velop.de/pages/resumedraft.action?draftId=681283194&draftShareId=75b2b48c-60fe-466e-a7ea-dc6d4f92f81e&)  
[Benutzte .yaml](https://github.com/LarsFischer97/CO-719/blob/main/doc.yaml)  

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
[ReDoc Configuration](https://github.com/Redocly/redoc#configuration)  
[ReDoc Prototype HTML](https://github.com/LarsFischer97/CO-719/blob/main/api_redoc.html)  
[ReDoc Setup](https://github.com/Redocly/redoc#deployment)  


## Zusatz


[<img src="https://i.imgur.com/3hFPgPP.png">]()
