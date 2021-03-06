# Font2vec : Learning font embeddings

## About
Our work successfully creates font embeddings that can be used for achieving the following goals:
1. Identify the fonts effectively from a given font image, and also suggest fonts similar to it.
2. Provide an interface to alter features of a font (such as thickness, width, alignment, etc). In addition to generating new fonts from the altered embedding, we also suggest similar fonts.
3. Combine multiple fonts, and visualize the output of the same by interpolating their embeddings.
4. Unsupervised labelling of fonts by clustering them effectively. This could augment performance of OCR tasks.

## Dataset
- We generated the dataset from scratch using [Google Fonts](github.com/google/fonts) and extra fonts were scraped from the web. 
- .ttf and .otf files are used to create 56 * 56 grayscale images were generated
- There are two datasets:
    - [2409 font images](https://drive.google.com/file/d/1sLe0Oyf4tP-XXG1OYpbTtC5YPbD3aa1Y/view?usp=sharing), generated from Google Fonts.
    ```
    gdrive_download 1sLe0Oyf4tP-XXG1OYpbTtC5YPbD3aa1Y gfont_ims.zip
    unzip gfont_ims.zip
    ```
    - [13,674 font images](https://drive.google.com/file/d/13-MaBAjnX2WY5xFwayYt73nm01QHywGJ/view?usp=sharing), generated from (Google Fonts + Fonts scraped from the web).
    ```
    gdrive_download 13-MaBAjnX2WY5xFwayYt73nm01QHywGJ font_ims_all.zip
    unzip font_ims_all.zip
    ```

    ```
    function gdrive_download () {
    CONFIRM=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate "https://docs.google.com/uc?export=download&id=$1" -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')
    wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$CONFIRM&id=$1" -O $2
    rm -rf /tmp/cookies.txt
    }
    ```
## Results 
### Identifying font in image
We pick 5 font images from our test set and find the closest font to each of them.

<img src= "Images/Screenshot from 2018-09-21 18-19-24.png" width="400">

### Interpolations between fonts
#### Font Clusters

- We performed K-means clustering (with k=8) on embeddings of all fonts 
- We then found the closest font to each cluster center

<br>
<img src= "Images/Screenshot from 2018-09-26 18-41-52.png" width="400">

#### 1D interpolations
- Row 1 - Outlined to Solid
- Row 2 - Condensed to Regular-Italic
- Row 3 - Serif to SansSerif
- Row 4 - Monospace to Cursive
- Row 5 - Thin to Bold.

<img src= "Images/Screenshot from 2018-09-21 18-17-07.png" width="400">

#### 2D interpolations
<img src= "Images/Grid - 1.png" width="200">
<img src= "Images/Grid - 5.png" width="200">

### Visualisation
- You can visualize the embeddings of all Google fonts [here](bit.ly/font2vec)
- This used Tensorboard Projector to project 32dim embeddings in 3D and 2D space with PCA and T-SNE
- Embeddings were generated by training a VAE on 13k fonts and using the encoder to predict these codes of Google fonts.

## Training
- After experimenting with RBMs and number of AE architectures, we eventually settled for the following variational autoencoder : <br>
    <img src="Model_VAE.png" width = 230 >
- It was trained for nearly 1500 epochs on Nvidia GTX 1050Ti. 
- Each step took ~350 microseconds and each epoch on the bigger dataset took ~4.7 seconds

