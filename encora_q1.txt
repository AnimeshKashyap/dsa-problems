const input = [6,13,22,18,0,3,45,57,5,12];

const findSecondLowest = (arr) => {
    // assuming minimum 2 numbers are given in the input else will return null
    if(!arr || arr.length < 2){
        return null;
    }

    let lowest;
    let sec_lowest;
    if(arr[0] > arr[1]){
        lowest = arr[1];
        sec_lowest = arr[0];
    }
    else{
        lowest = arr[0];
        sec_lowest = arr[1];
    }
    let idx = 2;
    while(idx < arr.length){
        let num = arr[idx];
        // Case 1: if current number (num) is shorter than lowest, both lowest and sec_lowest will be shifted
        if(num < lowest){
            sec_lowest = lowest; // lowest will become new sec_lowest
            lowest = num; // new lowest found
        }
        else
        // Case 2: if current number (num) is between lowest and sec_lowest, only sec_lowest will be updated
        if(num > lowest && num < sec_lowest){
            sec_lowest = num;
        }
        idx++;
    }
    return sec_lowest;
}

console.log(findSecondLowest(input));