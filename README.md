# DiskDict
DiskDict is a hashtable on your hard drive. It allows you to store many key/value pairs with very little RAM usage and interact with them as you would a Python dictionary, all the while never storing more than two key/value pairs in memory simultaneously. The key/value pairs can be anything that's serializable (you specify the serializer and deserializer, with repr and eval as the default). Since we're operating on the disk, big O of all operations are dependent on the host operating system's file access complexities.

##How does it work?
DiskDict works the same way a separate chaining hashtable does, only it's on disk. In this case, a bucket is a file and key/value pairs are stored in the file the key gets hashed to. Keys and values are serialized, so you can store any object as either the key or value that's serializable, not just immutable objects as in a Python dictionary. DiskDict uses the xxhash 64 bit hash function, which is incredibly fast: https://github.com/Cyan4973/xxHash You can also specify the serializer and deserializer to use, so you can use ujson, cPickle, repr/eval, or anything else that serializes and deserializes correctly according to your task.

##How many pairs can I store?
DiskDict is a hashtable and the bins are files. This means that the total number of pairs you can store depends on the filesystem you're using. The following table assumes there will be no collisions in the 64-bit hash space DiskDict uses. If there are collisions, more pairs can be stored.

|Filesystem | Max pairs                            |
|:----------|:-------------------------------------|
|ext4       |2^32-1                                |
|ext3       |min(volumeSize / 2^13, numberOfBlocks)|
|ext2       |10^18 (performane issues past 10,000) |
|NTFS       |2^32-1                                |
|FAT32      |65,535                                |

So use either ext4 or NTFS :)

##Example Usage
```python
>>> from disk_dict import DiskDict
>>> from numpy import array
>>> dd = DiskDict('my_disk_dict')
>>> a = array([[1,2],[3,4]])
>>> dd[a] = 'I was pointed to by ' + repr(a)
>>> dd[a]
'I was pointed to by array([[1, 2],\n       [3, 4]])'
>>> del dd[a]
>>> dd[a]
>>>
```

##Installation
pip install disk_dict

###Links
PyPI: https://pypi.python.org/pypi?name=disk_dict&:action=display
Github: https://github.com/AWNystrom/DiskDict/
