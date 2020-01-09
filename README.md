# Da3SH Module Web Workers

Enabling _Web Workers_ in **Da3SH Modules** requires a global level object:

 - `ashivaModuleWorkers = {}`

and two functions:

 - `function new_Worker (workerData) {}`
 - `function getNewWorker (workerJSON, customObject) {}`
 
**See below:**

```
  //*************************************//
 // DA3SH MODULES :: ENABLE WEB WORKERS //
//*************************************//

let ashivaModuleWorkers = {};

function new_Worker (workerData) {

  // 1) NEED TO FIGURE OUT HOW WORKERS CAN SPAWN SUB-WORKERS
  // 2) LOOK INTO importScript('file1.js'); and self.importScript('file2.js', 'file3.js');
  // 3) NEED TO DISTINGUISH BETWEEN DEDICATED WORKERS AND SHARED WORKERS
  // 4) NEED TO FIGURE OUT HOW TO DEAL WITH new Worker('my-module.mjs', { type : module });
  // 5) NEED TO LEARN ABOUT WORKLETS

  let [workerName, ashivaModule, ashivaPublisher] = [workerData.workerName, workerData.ashivaModule, workerData.ashivaPublisher];
  workerData.workerAddress = `${ashivaModule}»by»${ashivaPublisher}»»»${workerName}`;

  getRemoteResponse(`/.assets/modules/${ashivaPublisher}/${ashivaModule}/datasheets/${workerName}on`, getNewWorker, workerData);
}

function getNewWorker (workerJSON, customObject) {

  let [workerName, workerAddress, workerFunction] = [customObject.workerName, customObject.workerAddress, customObject.workerFunction];
  
  let workerScript = renderScript(workerJSON);
  let workerBlob = new Blob([workerScript], {type: 'text/js-worker'});
  let workerURL = window.URL.createObjectURL(workerBlob);

  ashivaModuleWorkers[workerAddress][workerURL] = workerFunction;

  let worker = new Worker(workerURL);
  customObject.workerFunction(worker, customObject);
}

console.log(ashivaModuleWorkers);



  //*************************************//
 // DA3SH MODULES :: ENABLE WEB WORKERS //
//*************************************//

let ashivaModuleWorkers = {};


function new_Worker (workerData) {
  
  let [workerName, moduleName, modulePublisher] = [workerData.workerName, workerData.moduleName, workerData.modulePublisher];
  let workerAddress = workerData.workerAddress = `${moduleName}»by»${modulePublisher}»»»${workerName}`;

  if (!ashivaModuleWorkers.hasOwnProperty(workerAddress)) {ashivaModuleWorkers[workerAddress] = {moduleName, modulePublisher, workerFunctions : []};}
  if (!workerData.hasOwnProperty('workerFunction')) {workerData.workerFunction = 'workerFunction_' + workerName.replace('--worker', '').replace('.js', '');}
  
  ashivaModuleWorkers[workerAddress].workerFunctions.push(workerData.workerFunction);
  getRemoteResponse(`/.assets/modules/${workerData.modulePublisher}/${workerData.moduleName}/datasheets/${workerName}on`, getNewWorker, workerData);
}


function getNewWorker (workerJSON, customObject) {

  let [workerName, workerAddress] = [customObject.workerName, customObject.workerAddress];

  let workerScript = renderScript(workerJSON);
  let workerBlob = new Blob([workerScript], {type: 'text/js-worker'});
  let workerURL = window.URL.createObjectURL(workerBlob);

  ashivaModuleWorkers[workerAddress].workerURL = workerURL;
  let worker = new Worker(ashivaModuleWorkers[workerAddress].workerURL);
  eval(customObject.workerFunction)(worker, customObject);
}
```

