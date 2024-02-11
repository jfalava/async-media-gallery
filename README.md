# Asynchronous Media Gallery

Web application that dynamically loads images and videos from a JSON file into a gallery.  
It allows users to tag images and filter the gallery content based on those tags.  
The gallery supports both images and videos and includes a zoomed view for images.

Check a demo [here](https://jfalava.github.io/async-media-gallery/).

## How It Works

* The "tag selector" menu initially renders,
1. A tag is clicked,
2. The card gallery info (image, description and related tag) is fetched from the data origin (the `img-sources.json`) based on the tag clicked,
3. The array is populated with that info,
4. The `addImageToGallery` function is called and renders the HTML gallery card with the array info.

### `fetchImageData`: fetches a JSON file with image sources and stores the data for later display

* Fetches image data from a JSON file (`img-sources.json`), processes it, and stores it in the `imageDataStore` array.  
* It then triggers the creation of tags based on the fetched data and adds event listeners.  
* Finally, it logs the loaded image data to the console.

> While the function itself is not marked as asynchronous, it deals with asynchronous operations through the `Fetch` API and Promise chaining.

### Tags: dynamically based on a predefined array of tag objects, they are a `div` each with a name and background color

* Tags are created based on the provided data and appended to the specified container element in the HTML document.
* Each tag is clickable and triggers an event that selects the clicked tag and loads relevant content associated with it.

#### `createTags()` CSS Classes

* `.tag`: styles each individual tag.
* `.tag-selected`: styles a tag when it's selected.
* `.tag-container`: styles the container where the tags are appended.
* `.tag:hover`: styles the container where the tags are appended.

### Gallery Population: Images and videos are added to the gallery based on their file type

#### Parameters (given by the`img-sources.json` file)

* `image`: An object representing the image or video to be added to the gallery. It should have the following properties:
  * `src`: A string representing the URL or path to the image or video file.
  * `description`: A string providing a description or caption for the image or video.

#### `addImageToGallery(image)` Behavior

1. Logs a message indicating the loading of the image or video to the console.
2. Determines the file extension of the provided image source to differentiate between images and videos.
3. Creates a container element `div` for the media item with the class `image-card`.
4. Depending on the file extension, creates either an `<img>` element for images or a `<video>` element for videos.
   1. For videos, it sets the controls attribute to allow user interaction and appends a `<source>` element with the appropriate source and type attributes.
5. Appends the media element (either `<img>` or `<video>`) to the media card.
6. Creates a separate `div` element for displaying image information (description or caption) with the class `image-info`.
7. Appends the image information to the media card.
8. Appends the completed media card to the gallery element on the webpage.

#### `addImageToGallery(image)` CSS Classes

* `.gallery-card`: styles the container for each gallery.
* `.gallery-item`: styles the media element (`<img>` or `<video>`).
* `.image-card`: styles the container for each image or video.
* `.image-info`: styles the container for image information (description or caption).

### `loadImagesForTag(tag)`: Clicking on a tag filters the gallery to only show items associated with that tag

This allows for dynamic filtering of images based on tags for then displaying it in the gallery.  

#### `loadImagesForTag(tag)` behavior

On click:  

1. It clears the existing gallery content: if there is content with a different `tag` being shown, it will be hidden.
2. Then filters the image data store for images with the specified tag.
    * You can change the behaviour of the filtering process by changing `imageDataStore.filter`.  
   Example: for an `and` clause:

   ```js
   imageDataStore.filter(image => {
   const imageTags = image.tags.toLowerCase().split(' ');
   // Check if all the keywords in tagKeywords are present in imageTags
   return tagKeywords.every(keyword => imageTags.includes(keyword));
   }).forEach(image => addImageToGallery(image));
    ```

3. Each filtered image is added to the gallery using the `addImageToGallery()` function.
4. An event listener is attached to the gallery, which listens for clicks.
   1. If the clicked element is an image (`<img>` tag), it triggers the modal display.

### Modal Functionality: clicking an image opens it in a modal view

#### Modal Functionality behavior

1. Upon clicking an image, a modal is shown containing the clicked image in a larger size.  
2. The modal is represented by a class `.modal`.  
3. The clicked image source is set as the source for the image displayed in the modal.  
4. The modal and the close button (with id `modal-dismiss`) are displayed.
5. The modal will be dismissed by clicking anywhere or if the <kbd>Esc</kbd> button is pressed.

#### Modal Functionality CSS Styles

* `.modal`: styles the new modal div. The modal is a new div that creates an illusion of a new zoomed image.  
This is an example of a modal style, that tries to reflect it:

```css
.modal {
  display: none;
  position: fixed;
  z-index: 1;
  padding-top: 50px;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.9);
}
```

* `#modal-dismiss`: styles the dismiss message.
  * Coded as an `id` instead of a `class` for tracking purposes, but you can change it by changing the script to `const modalDismiss = document.querySelector('.modal-dismiss');` and using a class instead

## Setup

To use this script, include the following elements in your HTML markup:

* An empty `div` with the ID `gallery` to hold the gallery items.
* An empty `div` with the ID `tagContainer` to hold the tag elements.
* A modal `div` structure for the image preview.  

Your JSON file (`img-sources.json`) should follow this format (the source `src` can be anywhere):

```json
[
  {
    "src": "path/to/image.jpg",
    "description": "Image description",
    "tags": "TagOne"
  },
  {
    "src": "path/to/video.mp4",
    "description": "Video description",
    "tags": "Tag2"
  }
]
```
