# Project 4 Design report

UW-Madison CS564 Database Management Systems

We use templates to reduce the amount of duplicate code. We use `L_T` and `NL_T`, which refer to leaf or non-leaf nodes; `P_T` and `RID_T`, which refer to `pageKeyPair<T>` and `RIDKeyPair<T>`. In this way we can pass information upwards from lower level in an efficient manner. We also have type T to indicate the data type in tuples, which is passed all the way down from the top level. T can be either int, double or char*.
The current rootnode is a leaf node. This is the case during the first few insertions when the root node is the only node in the tree. In this special case we call the `insertRootLeaf()` function.
We do this because in the following code, we traverse the entries in leaf node linearly, and change current page number if the entires on one page have been iterated through. If the next page obtained by `currLeaf->rightSibPageNo` has a page number of zero, that means we have already gone through all entires in B+tree. We set the current page number to zero and return. Then when scanNext is called one iteration later, `IndexScanCompleteException` will be thrown and the scan will exit.

## Test cases:
In addition to the provided tests and `errorTests()` function, we created two additonal tests in our main.cpp file. One is for reading existing B+tree Index from previously created files. To test this function, user need to first call `badgerdb_main` and enter command line option 6, this will create three B+Tree Indexs for consecutive numbers from 0 to 5000. Then user can call normal tests to see if the tree behaves normally when the indexes are read from disk.
We added another test to see if our tree can handle range from (-1000,6000), and it behaved as expected. The additional code can be seen in individual type tests like `intTests()` function.

We also tested cases where non-leaf splits occurred. To shorten our runtime, we shrinked the nonleaf node capacity to 10 to create a three-level B+Tree. We also tested the case where inserted keys are sparse, we did this by generating 3000 numbers with range from 0 to 100000. However these two test cases are not compatible with other cases, and for time purposes we did not include them in our final main.cpp file. An code snippet for sparse test is included in our handin directory.

## Duplicate key scenario:
If duplicate keys are allowed,for nonleaf nodes, the mechanism will be the same, however for leafnode entries we can create an additional layer beneath the original leafnode. Each leafnode entry will now point to a position in the additonal layer, where the entries after the position will store values greater or equal to the key value in leafnode entry. By creating a list of duplicate keys underneath the original leafnode, the shape property above the addtional level is maintained. In the lowest level, key values are stored in sorted order, and we can do range search similar to the original design with similar speed.

