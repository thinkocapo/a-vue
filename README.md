# sentry-demos/vue

## Project setup
1. Verify Node.js and NPM installation or install https://nodejs.org/en/
```
node -v
```
```
npm -v
```
2. Verify Sentry CLI is installed
```
sentry-cli -V
```
Or download the Sentry CLI
```
npm install @sentry/cli
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
5. Set other variables in your .env file; use the .env.example file as a reference

## Run
1. Run in terminal with
```
npm run deploy
```
Note: Source maps are uploaded with ```npm run deploy```. During development or testing, you may want to edit ```package.json```'s ```"predeploy": "npm run build && make setup_release",``` to do ```npm run build``` without the ```make setup_release``` method.


