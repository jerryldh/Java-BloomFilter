java-bloomfilter is a stand-alone Bloom filter implementation written in Java. It is intended to be easy to include in existing projects without the overhead of additional libraries. The first version was inspired by a blog entry by [Ian Clarke](http://blog.locut.us/2008/01/12/a-decent-stand-alone-java-bloom-filter-implementation/).


Bloom filters
-------------
Bloom filters are usually used for set membership tests. They are fast and space-efficient at the cost of accuracy. Although there is a certain probability of error, Bloom filters never produce false negatives.

If you are new to Bloom filters, you can learn more about them in [this tutorial](http://llimllib.github.com/bloomfilter-tutorial/). A more comprehensive overview is available from [Wikipedia](http://en.wikipedia.org/wiki/Bloom_filter).

Examples
--------

To create an empty Bloom filter, just call the constructor with the required false positive probability and the number of elements you expect to add to the Bloom filter. 

```java
double falsePositiveProbability = 0.1;
int expectedNumberOfElements = 100;

BloomFilter<String> bloomFilter = new BloomFilter<String>(falsePositiveProbability, expectedNumberOfElements);
```
The constructor chooses a length and number of hash functions which will provide the given false positive probability (approximately). Note that if you insert more elements than the number of expected elements you specify, the actual false positive probability will rapidly increase.

There are several other constructors available which provide different levels of control of how the Bloom filter is initialized. You can also specify the Bloom filter parameters directly (bits per element, number of hash functions and number of elements).

After the Bloom filter has been created, new elements may be added using the add()-method.

```java
bloomFilter.add("foo");
```

To check whether an element has been stored in the Bloom filter, use the contains()-method. 

```java
bloomFilter.contains("foo"); // returns true
```

Keep in mind that the accuracy of this method depends on the false positive probability. It will always return true for elements which have been added to the Bloom filter, but it may also return true for elements which have not been added. The accuracy can be estimated using the expectedFalsePositiveProbability()-method.

Put together, here is the full example.

```java
double falsePositiveProbability = 0.1;
int expectedSize = 100;

BloomFilter<String> bloomFilter = new BloomFilter<String>(falsePositiveProbability, expectedSize);

bloomFilter.add("foo");

if (bloomFilter.contains("foo")) { // Always returns true
    System.out.println("BloomFilter contains foo!"); 
    System.out.println("Probability of a false positive: " + bloomFilter.expectedFalsePositiveProbability());
}

if (bloomFilter.contains("bar")) { // Should return false, but could return true
    System.out.println("There was a false positive.");
}
```

Changes
-------
### 1.0
* Improved the speed of the add() and contains()-methods. The speed increase is around 4-5 times on my computer,, but the actual increase may vary from system to system.
* Added benchmark code to test future code optimizations.
* Fixed a bug in add() where numberOfAddedElements was incorrectly increased twice when adding other elements than byte-arrays. Updated test.
* Moved project to Github

### 0.9.3
* New constructor for estimating bitSetSize from a given false positive probability.
* New constructor for specifying bits per element, elements and hash functions (c, n, k) directly.
* Added getExpectedBitsPerElement() and getBitsPerElement()
