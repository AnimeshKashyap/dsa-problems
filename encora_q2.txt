const baseUrl = "https://api.potterdb.com"
const apiUri = {
    "books": "/v1/books",
    "book": "/v1/books/{bookId}",
    "chapters": "/v1/books/{bookId}/chapters",
    "chapter": "/v1/books/{bookId}/chapters/{chapterId}"
}

// made this handler to have single point of error handling for API calls
const getApiHandler = async (url) => {
    if(!url || typeof(url) !== "string"){
        console.error(`Invalid URL specified: ${url}`);
        return
    }

    try{
        // Call API
        let res = await fetch(url);
        // Convert res to JSON
        let data = await res.json();
        return data;
    }
    catch(exc){
        console.error(`Exception during API call againt url: ${url}, exception: ${exc}`);
        return
    }

}

// construct URL
(async () => {
    let booksUrl = baseUrl + apiUri["books"];
    let booksData = await getApiHandler(booksUrl);
    if(!booksData || !booksData["data"] || !booksData["data"].length){
        return;
    }
    let lastBook = booksData["data"][booksData["data"].length-1]; // Getting the last book's data
    let lastBookId = lastBook["id"];
    
    // Null checks
    if(!lastBook || !lastBook["relationships"] || !lastBook["relationships"]["chapters"]|| !lastBook["relationships"]["chapters"]["data"]){
        console.error(`Chapters' IDs not present in the fetched book data for book id: ${lastBookId}`);
        return;
    }
    let chaptersData = lastBook["relationships"]["chapters"]["data"]; // Getting chapter ids from last book's data
    let lastChapter = chaptersData[chaptersData.length - 1];
    let lastChapterId = lastChapter["id"];
    let chapterUrl = baseUrl + apiUri["chapter"].replace("{bookId}", lastBookId).replace("{chapterId}", lastChapterId);
    let lastChapterData = await getApiHandler(chapterUrl);
    
    // Null checks
    if(!lastChapterData || !lastChapterData["data"] || !lastChapterData["data"]["attributes"]){
        console.error(`Attributes not present in the fetched data for chapter id: ${lastChapterId} (url: ${chapterUrl})`);
        return;
    }

    let summary = lastChapterData["data"]["attributes"]["summary"];
    console.log(`Last Book ID: ${lastBookId}`);
    console.log(`Last Chapter ID: ${lastChapterId}`);
    console.log(`Last Chapter Summary: ${summary || "<empty>"}`); // Summary values are present as null string in the response
    // showing as <empty> for better visual representation
    console.log(`\nTo verify, go to URL: ${chapterUrl}`);
})();

