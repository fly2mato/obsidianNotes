The main difference is that 

*unpersist(true)* will block your computation pipeline in the moment it reaches that instruction until it has finished removing the contents of the RDD/DF/DS. 

While *unpersist(false)* or just *unpersist()* will just put a mark on the RDD/DF/DS which tells spark it can safely deletes it whenever it needs to - note that if spark needs memory for a computation it may even deletes cached data that is not marked to be deleted. I think is better to just leave it to spark, it might not even need to delete it - thus you will lose time by forcing its deletion.

