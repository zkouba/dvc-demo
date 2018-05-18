# Data Version Control (DVC) Tutorial
https://blog.dataversioncontrol.com/data-version-control-tutorial-9146715eda46

## How to reproduce
* Download the dataset 
  ```bash
  S3_DIR=https://s3-us-west-2.amazonaws.com/dvc-share/so
  wget -P data $S3_DIR/100K/Posts.xml.tgz
  ```
*   Run DVC reproduce command
  ```bash
  dvc repro
  ```

