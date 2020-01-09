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

