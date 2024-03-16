// Function to download image
function downloadImage(url, filename, folderName, index) {
    fetch(url)
        .then(response => {
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            return response.blob();
        })
        .then(blob => {
            const objectURL = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = objectURL;
            a.setAttribute('download', `${folderName}/${filename}`);
            a.style.display = 'none';
            document.body.appendChild(a);
            a.click();
            window.URL.revokeObjectURL(objectURL);
            console.log(`Image ${index} downloaded: ${filename}`);
        })
        .catch(error => console.error(`Error downloading image ${index}:`, error));
}

// Function to save all images from the page
function saveAllImages(folderName) {
    const images = document.querySelectorAll('img');
    let foundCount = 0;
    let downloadedCount = 0;

    images.forEach((image, index) => {
        const imageUrl = getOriginalImageUrl(image);
        if (imageUrl) {
            foundCount++;
            const filename = getImageFilename(imageUrl);
            downloadImage(imageUrl, filename, folderName, foundCount);
            downloadedCount++;
        }
    });

    console.log(`Total images found: ${foundCount}`);
    console.log(`Total images downloaded: ${downloadedCount}`);
}

// Function to get the original image URL
function getOriginalImageUrl(image) {
    if (image.dataset.src) {
        return image.dataset.src; // Lazy-loaded image
    } else {
        return image.src; // Regular image
    }
}

// Function to get the image filename from its URL
function getImageFilename(url) {
    const splitUrl = url.split('/');
    return splitUrl[splitUrl.length - 1]; // Extract filename from URL
}

// Function to scroll down to load more images
function scrollDown() {
    window.scrollTo(0, document.body.scrollHeight);
}

// Specify folder name for saving images
const folderName = 'IMG';

// Call the function to save all images
saveAllImages(folderName);

// Automatically scroll down to load more images
scrollDown();

// Refresh the script in a loop to catch new images
setInterval(() => {
    saveAllImages(folderName);
    scrollDown();
}, 5000); // Refresh every 5 seconds (adjust as needed)
