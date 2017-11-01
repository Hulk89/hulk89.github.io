```python
# -*- coding: utf8 -*-
import sys
import pymongo

reload(sys)
sys.setdefaultencoding('utf8')

mng_client = pymongo.MongoClient('localhost', 27017)
mngDB = mng_client['newsDB']
mngCollection = mngDB['rawCollection']

cursor = mngCollection.find({})

number = mngCollection.count()
count = 0
for doc in cursor:
    text = doc["text"]
    id_ = doc["_id"]
        
    if not "wordList" in doc:
        wordList = getWordsFromArticle(text)
        wordListReduced = [word for word in wordList if hulkW2V.isInW2C(word)]
        mngCollection.update({'_id': doc["_id"]}, {'$set': {'wordList' : wordListReduced}})
        count += 1
        print "%d/%d processed" %( count, number)

```