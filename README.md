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

// Function to save all main images from the visible area of the page
function saveMainImages(folderName) {
    const images = document.querySelectorAll('img');
    let foundCount = 0;
    let downloadedCount = 0;

    images.forEach((image, index) => {
        if (isImageInViewport(image) && isMainContentImage(image)) {
            foundCount++;
            const imageUrl = getOriginalImageUrl(image);
            if (imageUrl) {
                const filename = getImageFilename(imageUrl);
                downloadImage(imageUrl, filename, folderName, foundCount);
                downloadedCount++;
            }
        }
    });

    console.log(`Total main images found: ${foundCount}`);
    console.log(`Total main images downloaded: ${downloadedCount}`);
}

// Function to check if the image is within the viewport
function isImageInViewport(image) {
    const rect = image.getBoundingClientRect();
    return (
        rect.top >= 0 &&
        rect.left >= 0 &&
        rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
        rect.right <= (window.innerWidth || document.documentElement.clientWidth)
    );
}

// Function to check if the image is main content based on attributes or other criteria
function isMainContentImage(image) {
    // You can add your own logic here to determine if the image is main content
    // For example, check if it's within a certain section of the page, or has specific attributes
    return true; // For demonstration, returning true for all images
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

// Specify folder name for saving images
const folderName = 'IMG';

// Call the function to save all main images
saveMainImages(folderName);

// Function to periodically check for new main images
setInterval(() => {
    saveMainImages(folderName);
}, 1000); // Check every second for new main images
