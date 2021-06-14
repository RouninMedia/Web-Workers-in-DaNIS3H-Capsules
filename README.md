# DaNIS3H Module Web Workers

Enabling _Web Workers_ in **DaNIS3H Modules** requires a global level object:

 - `ashivaModuleWorkers = {}`

and two functions:

 - `function new_Worker (workerData) {}`
 - `function getNewWorker (workerJSON, customObject) {}`
 
**See below:**

```
  //*************************************//
 // DaNIS3H MODULES :: ENABLE WEB WORKERS //
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

```
