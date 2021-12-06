# sentry-demos/a-vue

## Context
This repo was created for a "7 minute lightning talk" demo. I needed to rename the repo to a-vue so it appears at the top of the ```Sentry.io > Settings > Integrations > Code Mappings > Add Code Mapping``` list (which is sorted alphabetically and the list doesn't show all available repos). 

## Project setup
1. Verify Node.js and NPM installation or install https://nodejs.org/en/
```
node -v
```
```
npm -v
```
2. Verify Sentry CLI installation or install ```npm install @sentry/cli```
```
sentry-cli -V
```
3. Verify vue installation or install https://cli.vuejs.org/guide/installation.html
```
vue --version
```
4. Set your SENTRY_AUTH_TOKEN
e.g. Terminal: 
```
export SENTRY_AUTH_TOKEN=<Your Auth Token>
```
5. Set other variables in your ```.env``` file; use the ```.env.example``` file as a reference

## Run
1. Run in terminal with
```
npm run deploy
```
Note: Source maps are uploaded with ```npm run deploy```. During development or testing, you may want to edit ```package.json```'s ```"predeploy": "npm run build && make setup_release",``` to do ```"predeploy": "npm run build",``` (without the ```make setup_release``` method).


