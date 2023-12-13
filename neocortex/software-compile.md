
### Compile
For the CS-2s, the flags should be set to `--arch=wse2` and `--fabric-dims=757,996` like the following:
```
cslc --arch=wse2 ./code.csl --fabric-dims=757,996 --fabric-offsets=1,1 --colors=x_in:1,b_in:2,y_out:3,Ax_out:4,sentinel:43 -o out
```
