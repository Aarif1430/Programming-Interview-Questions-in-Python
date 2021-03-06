# QUESTION
Given an integer W that represents number of words and unlimited space complexity.
Write functions insert(word) and getMostPopularWord() such that getMostPopularWord() will always return the most popular word in the last W number of words.
Create two solutions that will optimize run-time complexity for either function while sacrificing the run-time for the other function. 

For example:
```
let W = 2
insert("A")
getMostPopularWord() => "A"
insert("B")
getMostPopularWord() => "B"
```

```
let W = 3
insert("A")
insert("A")
getMostPopularWord() => "A"
insert("B")
getMostPopularWord() => "A"
insert("B")
getMostPopularWord() => "B" // since the first inserted "A" is out of the scope of the last 3 words
```

# SOLUTION 1 to optimize getMostPopularWord()
We will use a queue of W size in-order to remember which was the oldest word that was added so we can decrement its popularity.
Then use a hash table that uses keys of words that return values of nodes in a sorted list.
Lastly, we will use a sorted list of nodes. Each node will have its word and its popularity.
In this way, we can use the hash table to increment and decrement the popularity of the nodes quickly then sorting them.

Here is the small trick, when a new word is added or its popularity increases, we will move the node in the sorted list to the front. Then create a custom sort function that will give priority to nodes in the front of the list when comparing two nodes with the same popularity. In this way, we can maintain order, which will allow us to get the most popular word at O(1) run-time and insert at O(nlog(n)) run-time.
  
```
from collections import deque

class solution(object):
    def __init__(self, n_words_to_hold):
        self.n_words_to_hold = n_words_to_hold
        self.word_queue = deque()
        self.word_to_sorted_hash = dict()
        self.sorted_popularity = list()
    
    def insert(self, word):
        if len(self.word_queue) == self.n_words_to_hold:
            oldest_word = self.word_queue.pop()
            self.decrement_word(oldest_word)
        self.word_queue.appendleft(word)
        self.increment_word(word)
        self.sorted_popularity.sort(custom_compare)
        print 'sorted {}: {}'.format(word, self.sorted_popularity)
        print self.word_queue
        
    def getMostPopularWord(self):
        if len(self.sorted_popularity) != 0:
            return self.sorted_popularity[0][1]
        return ''
    
    def increment_word(self, new_word):
        popularity = 1
        if new_word in self.word_to_sorted_hash:
            node = self.word_to_sorted_hash[new_word]
            popularity += node[0]
            self.sorted_popularity.remove(node)
        new_node = [popularity, new_word]
        self.word_to_sorted_hash[new_word] = new_node
        # Trick to insert at beginning so that the sort will place priority for elements in the beginning
        # There might be a better way than this
        self.sorted_popularity.insert(0,new_node)
        
    def decrement_word(self, old_word):
        old_node = self.word_to_sorted_hash[old_word]
        old_node[0] -= 1
    
def custom_compare(a, b):
    if b[0] >= a[0]:
        return 1
    return -1

test = solution(2)
test.insert('A')
test.insert('B')
test.insert('A')
print test.getMostPopularWord()
```

# SOLUTION 2 to optimize insert(word)
For this solution, we will take a slightly different approach. We will use a frequency counter that increments as we add words. Using this, we can insert words into a hash table with keys of words and values of popularity and the frequency counter. Obvious increment and decrements will be performed using the hash table and a queue, as explained above. Then when getMostPopularWord is called, its a simple one pass through the hash table's values to find the most popular word O(n) while having insert at constant run-time.

```
from collections import deque
import sys

class popular_words(object):
    def __init__(self, n_words_to_hold):
        self.n_words_to_hold = n_words_to_hold
        self.word_queue = deque()
        self.word_to_popular_counter = dict()
        self.counter = -sys.maxint - 1
        
    def insert(self, new_word):
        if self.n_words_to_hold == 0:
            return
        if new_word in self.word_to_popular_counter:
            self.word_to_popular_counter[new_word][0] += 1
            self.word_to_popular_counter[new_word][1] = self.counter
        else:
            self.word_to_popular_counter[new_word] = [1, self.counter]
        if len(self.word_queue) == self.n_words_to_hold:
            oldest_word = self.word_queue.pop()
            self.word_to_popular_counter[oldest_word][0] -= 1
        self.word_queue.appendleft(new_word)
        self.counter += 1
        
    def getMostPopularWord(self):
        most_popular_num = 0
        most_popular_word = ''
        lastest_counter = -sys.maxint - 1
        for word, popular_counter in self.word_to_popular_counter.items():
            popularity, counter = popular_counter
            if popularity > most_popular_num:
                most_popular_num = popularity
                lastest_counter = counter
                most_popular_word = word
            elif popularity == most_popular_num:
                if counter >= lastest_counter:
                    lastest_counter = counter
                    most_popular_word = word
        return most_popular_word
    
test = popular_words(4)
test.insert('A')
test.insert('B')
test.insert('B')
test.insert('A')
print test.getMostPopularWord()
```

# FOLLOW UP QUESTION
Can you get both functions to run at O(1) most of the time?

# SOLUTION TO FOLLOW UP QUESTION

```
```
