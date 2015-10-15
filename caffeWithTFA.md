
Summary:
=============
* Using Caffe will give sparse feature which will lead to 0 correct. 
* Try to fuse layers of say 'norm2' to one layer by summing gives 0 correct. a feature of a image is nxn
* Try to fuse layers of say 'norm2' to one layer by PCAing layers gives 0 correct. dataCaffePCA
* Try to PCAing flattened feature of images (40k dimention) to 150 dimension will give 6% correctness, 
    - not too much improve from raw image TFA

Question:
==
* Are there any other better way to deal with sparse feature for TFA?
* I used PCA in a right way?
* other way instead of TFA?

# Testing results:


## Testing 20151009v1
### Config
* bvlc_reference net
* car part 2,3,6
* Demo0, nFacors: [4 8 16 32 64]
* PCA

### Summary:
Layer | maxCrr(22) | maxCrr(33) | maxCrr(66) |face'hrql'(100)
--- | --- | --- | --- | ---
fc8 | <5 % |  | 4 @8| 8 @4
fc7 | 5 % | 12 @32 | 8 @4 | 6 @4
fc6 | 7 %  |  |  | 
pool5 | 7 % | 20 @32  |10 @32 |  8@64
conv5 | 10%   | 12 @16  | 8 @32 | 6 @8 <br> (face319: 4%)
conv4 | 10%  |   | | 
conv4 | 10%   |   | | 
norm2 | 12%   | 10 @4  | 10 @32 | 6 @16 <br> (face319: 3%)
norm1 | 13%   | 14 @32  | 16@64 | 4 @4 <br> (face319: 2%)
pool1 | 5%   |   | | 
conv1 | 7 % |    | | 
rawImage| | | | 26%32 <br>(face319: 53% @64 )

## Testing 20151009v2
### Config
* google net
* car part 2
* Demo0, nFacors: [4 8 16 32 64]
* PCA

### Summary:
Layer | maxCorrect | nFactor
--- | ---  | ---
pool5/7x7_s1 | 4%   | 4
pool3/3x3_s2 | 4%   | 4
pool2/3x3_s2 | 4%   | 32
conv2/norm2 | ?   | 
pool1/norm1 | 10 % | 32

The more abstract the worse of the result. 
Why?
* Because wheel has very few abstract concept.
* Because TFA does not like abstract feature
* Because PCA transform does not welcome abstract concept

## Testing 20151014v1
### Config
* refnet
* face
* Demo0, nFacors: [4 8 16 32 64]
* PCA

### Summary:
noted in 20151009v1
The DL does a bad job even for face images, which is impossible. 
Why?
* PCA goes wrong (most likely)
    * must be. since 'data' layer also gived only 2%
    * However, I don't know why data layer with no PCA will also give low rate, maybe bug?
* Bug?
    * the data iamge recreates odd thing.
        * transform? in_?
        * iCol = net.blobs[bloblayer].data[indexImage,:].flatten('F') This part makes wrong layer
            * With .transpose([1,2,0]) the flatten can do within layer column , row, then layer.
            * NEXT: play with the new feature expression column
            * NEXT: judge howmuch tranformer and in_ we need. Maybe ask Kien?
        * else?
