# Util
## CleverMerge
- When two object is passed as input, it returns the merged value. The operation is stored in cache as `WeakMap`.
- The advantage of using `WeakMap` is that the references are weakly associated and hence aiding in garbage collection unlike Arrays.
- The keys can be of type `Object`