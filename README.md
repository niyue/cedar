# About
Cedar implements an updatable double-array trie [1,2,3], which offers fast update/lookup for skewed queries in real-world data, e.g., counting words in text or mining conjunctive features in a classifier. Its update and lookup speed is comparable to (hard-to-serialize) hash-based containers (std::unordered_map) or modern cache-conscious tries [3,4] and even faster when the queries are short and skewed (see performance comparison).

It is developed by Naoki Yoshinaga at Yoshinaga Lab., IIS, University of Tokyo.
http://www.tkl.iis.u-tokyo.ac.jp/~ynaga/cedar

# Features
* Fast update for skewed/ordered queries: Cedar can incrementally build a double-array trie from a given keyset in time that is less than the other double-array trie libraries. As a dynamic container, the ordered insertion is 3x faster than std::unordered_map, while the random insertion is comparable.
* Fast lookup for skewed/ordered queries: Cedar, double-array trie, offers 2-30x faster skewed lookup than std::unordered_map, while the random lookup is comparable.
* Serialization (load and store): Cedar can immediately de/serialize the resulting trie from/into disk as immutable/mutable dictionary, thanks to its simple, pointer-free data structure based on two (or three) one-dimensional arrays.
* Small working space: Cedar, double-array trie, compactly stores a keyset that shares common prefixes, and it needs smaller working space (in building a trie) than other trie libraries that support serialization. If a static trie suffices and you mind the size of a resulting trie, I recommend darts-clone (same lookup speed, half size), or marisa-trie.
* Native support for 4-byte record: Cedar stores up-to four-byte record in type specified by the first template parameter. The second and third template parameters specify two exceptional values that indicate specific lookup failures; no value or no path. The default exceptional values for no value / path are -1 / -2 for int record and NaN values for float record. You can of course associate a five- or more byte record with a key by preparing a value array and store its index (int) in the trie.
* Sorted keys: The keys in a trie are alphabetically sorted as in std::map unless a user sets the third template parameter SORT_KEYS to false (slightly faster update).
* Parameter to control space/time trade-off for binary keyset [2]: Setting a larger value to the fifth template parameter lets cedar thoroughly seek for empty elements in a trie to reduce its size. The default value usually gives a good trade-off in building a trie with low branching factor or fan-out. Currently, a binary key with '\0' inside is not supported.
* Three trie implementations: a (normal) trie, a reduced trie [3] (compact size and faster look-up for short keys), a minimal-prefix trie (compact size for long keys). A reduced trie is enabled if you put #define USE_REDUCED_TRIE 1 before #include <cedar.h>, while a minimal-prefix trie is enabled if you #include <cedarpp.h> instead of cedar.h.
* Rule of thumb: First consider to use a (versatile) minimal-prefix trie (#include <cedarpp.h>) when you wonder which trie to use for your datasets or benchmark experiments with various kinds of keys. Only when the keys are short, use a normal trie (#include <cedar.h>). Use the reduced trie (#include <cedar.h> preceded by #include USE_REDUCED_TRIE) cautiously since it has some limitations on the values as stated above. The normal trie and the reduced trie are not meant for long keys.
* Simple, short, portable code: Ceder is implemented as a C++ header in ~600 lines, without depending on C++-specific headers such as standard template libraries.

# License
* It is licensed under one of the following:
  * BSD
  * GPL
  * LGPL

# How to pronounce 'cedar'
The library is named after cedar, the most popular kind of tree in Japan; so just pronounce the same as cedar in English.

# References
1. J. Aoe. An efficient digital search algorithm by using a double-array structure. IEEE Transactions on Software Engineering 15(9):1066--1077. 1989.
2. S. Yata and M. Tamura and K. Morita and M. Fuketa and J. Aoe. Sequential Insertions and Performance Evaluations for Double-arrays. Proc. the 71st National Convention of IPSJ, pp. 1263--1264. 2009.
3. N. Yoshinaga and M. Kitsuregawa. A Self-addaptive Classifier for Efficient Text-stream Processing. Proc. COLING 2014, pp. 1091--1102. 2014.
4. D. R. Morrison. PATRICIA -- practical algorithm to retrieve information coded in alphanumeric. Journal of the ACM. 15(4): 514--534. 1968.