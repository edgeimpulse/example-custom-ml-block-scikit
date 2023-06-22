# Custom Scikit Learn ML block example for Edge Impulse

This repository is an example on how to [add a custom learning block](https://docs.edgeimpulse.com/docs/adding-custom-transfer-learning-models) to Edge Impulse. This repository contains a small logistic regression model built with Scikit Learn, and an inference function written in [JAX](https://jax.readthedocs.io/en/latest/).

As a primer, read the [Custom learning blocks](https://docs.edgeimpulse.com/docs/edge-impulse-studio/learning-blocks/adding-custom-learning-blocks) page in the Edge Impulse docs.

> **Note on epochs**: You'll want a lot of training cycles (1,000+ or so) here.

## Inference function in JAX?

Custom ML blocks in Edge Impulse need to output TFLite files, which is not possible from scikit-learn. One way around this is by re-implementing the inference function using [JAX](https://jax.readthedocs.io/en/latest/), then compiling that down to TFLite. This is done in `minimal_predict_proba` in [train.py](train.py) and should be relatively straight forward for many scikit-learn functions.

If you need help porting these functions, or if there's any TFLite ops in your final model that are not supported by the EON Compiler (so you cannot run on device), then please let us know on the [forums](https://forum.edgeimpulse.com).

## Running the pipeline

You run this pipeline via Docker. This encapsulates all dependencies and packages for you.

### Running via Docker

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/).
2. Install the [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation) v1.16.0 or higher.
3. Create a new Edge Impulse project, and add data from the [continuous gestures](https://docs.edgeimpulse.com/docs/continuous-gestures) dataset.
4. Under **Create impulse** add a 'Spectral features' processing block, and a random ML block.
5. Open a command prompt or terminal window.
6. Initialize the block:

    ```
    $ edge-impulse-blocks init
    # Answer the questions, select "other" for 'What type of data does this model operate on?'
    ```

7. Fetch new data via:

    ```
    $ edge-impulse-blocks runner --download-data data/
    ```

8. Build the container:

    ```
    $ docker build -t custom-ml-keras .
    ```

9. Run the container to test the script (you don't need to rebuild the container if you make changes):

    ```
    $ docker run --rm -v $PWD:/app custom-ml-keras --data-directory /app/data --epochs 30 --learning-rate 0.01 --out-directory out/
    ```

10. This creates two .tflite files and a saved model ZIP file in the 'out' directory.

#### Adding extra dependencies

If you have extra packages that you want to install within the container, add them to `requirements.txt` and rebuild the container.

#### Adding new arguments

To add new arguments, see [Custom learning blocks > Arguments to your script](https://docs.edgeimpulse.com/docs/edge-impulse-studio/learning-blocks/adding-custom-learning-blocks#arguments-to-your-script).

## Fetching new data

To get up-to-date data from your project:

1. Install the [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation) v1.16 or higher.
2. Open a command prompt or terminal window.
3. Fetch new data via:

    ```
    $ edge-impulse-blocks runner --download-data data/
    ```

## Pushing the block back to Edge Impulse

You can also push this block back to Edge Impulse, that makes it available like any other ML block so you can retrain your model when new data comes in, or deploy the model to device. See [Docs > Adding custom learning blocks](https://docs.edgeimpulse.com/docs/edge-impulse-studio/organizations/adding-custom-transfer-learning-models) for more information.

1. Push the block:

    ```
    $ edge-impulse-blocks push
    ```

2. The block is now available under any of your projects via **Create impulse > Add new learning block**.
