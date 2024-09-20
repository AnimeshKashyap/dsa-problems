const express = require("express");
const app = express();

// Explanation: we will use worker threads module of Node.js to create a new thread for the CPU
// intensive task and to process it without blocking the MAIN event loop
const { Worker } = require("worker_threads");

app.get("/image-process", (req, res) => {
    const newThread = new Worker("./cpu-intesive-process.js"); // CPU-Intensive task will have to be pushed to a different file
    newThread.on("message", (msg) => {
        res.send(msg);
    })
    newThread.on("error", (err) => {}); // for error handling (gives error as parameter), if required
    newThread.on("exit", (code) => {}); // for handling thread termination (gives exit code as parameter), if required
});


// cpu-intensive-process.js <- Separate file
const { parentPort } = require('worker_threads');

// Blocking CPU-intensive task: image processing
function processImage() {
    for (let i = 0; i < 100000000; i++) {
        // Simulating image processing (CPU intensive task)
    }
    return "Image processed"; // this will be passed to message callback on the worker
}

// Send the result back to the main thread
parentPort.postMessage(processImage()); // Calling the process to execute the CPU-intensive task