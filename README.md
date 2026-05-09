# matmul

Example project demonstrating how to use a pre-built custom Docker base image with the Hive. The task itself is optimizing double-precision matrix multiplication (C = A*B, row-major).

See https://docs.hiverge.ai/gettingstarted/custom-base-image for details on custom base images.

## Structure

    *.cpp, *.h       -- evolved source files
    evaluator.py     -- builds, runs, parses output into Hive fitness JSON
    Dockerfile       -- custom base image (pre-compiles everything into /app)
    hive.yaml        -- experiment configuration
    hive-local.yaml  -- experiment configuration, demonstrating patching the
                        evaluator.

## Build & Run

These are the instruction to build and run this project locally

    make        # produces ./matmul
    ./matmul 2048 1536 2560

## Docker image

The first step towards a Hive experiment with a custom image is to create the
Docker image, see Dockerfile. Then build the image and push it to the Hive.

    docker build -t matmul:latest .
    hive push image matmul:latest hive:matmul:latest

## Run Hive Experiment

Now we're ready to run an experiment:

    hive create exp -c hive.yaml experiment_name=matmul-
    hive dashboard

and then follow its progress on the dashboard.


## Refine Hive Experiment

Often the evaluation script will need some tweaks. Now let's assume that we
don't want to change the Docker file, rebuild it and push it. In that case we
can simply edit the `evaluator.py` and include it as an overlay, see
`hive-local.yaml`.

    nvim hive-local.yaml
    hive create exp -c hive-local.yaml experiment_name=matmul-

and the modified `evaluator.py` will be sent to the Hive where it will be
copied into the Docker image. The experiment will then run with the modified
evaluation script.

## Handling Data
Data can be either included into the Docker image; or uploaded as an overlay.
