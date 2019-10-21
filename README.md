# Grammar corrector modified from spelling corrector

### Goal:Peter Norvig implemented a lightweight project to fix spelling mistakes. Now I modify it to a grammar corrector.
<br>
<b>input</b> : a phrase with less than or equal to 5 words
<br>
<b>output</b> : grammatically corrected phrase <b>if</b> input has any mistakes <b>esle</b> input
<br>

### Algorithm and code highlight:
<br>
1. Take the input and parse it into an array of words

```python
    words = ngram.split()
    
```
<br>
2. For each word in words, check if the word is in model.txt and records its index in words. If it is in words, words may have potential grammatical mistakes. Check model.txt to see which moditification(Insert, delete, or replace) should be apllied to this case. 

```python
for w_idx, word in enumerate(words):
        corr_arr = channel_model(word, model)
        for corr in corr_arr:
```
<br>

> case1:insert 
<pre>
    Find the position where the in_word should be inserted and then insert it.
</pre> 
```python
            if corr[0] == 'I': #需insert的情況
                in_idx = corr[2]
                in_word = corr[1]
                in_pos = w_idx + in_idx #插入的位置
                if in_idx<0: #做此修正插入位置才正確
                    in_pos+=1   
                if in_pos>=0 and in_pos<=len(words):
                    store.append(' '.join(words[:in_pos]+[in_word]+words[in_pos:]))
```

> case 2:delete
<pre>
    Check if the word we are going to delete is in words. If it is, delete is.
</pre>
```python
            elif corr[0] == 'D': #需delete的情況
                d_word = corr[1]
                d_idx = corr[2]
                d_pos = w_idx + d_idx #del的位置
                
                if d_pos>=0 and d_pos<len(words) :
                    
                    new_words = words[:] #使用deep copy
                    if new_words[d_pos] == d_word:
                        del new_words[d_pos]
                        store.append(' '.join(new_words))
```
>case 3:replace
<pre>
    Check if the word we are going to replace is in words. If it is, replace it with the word indicated in model.
</pre>
```python     
            else:      #需replace的情況
                br_word = corr[1]
                r_word = corr[2]
                if br_word in words:
                    br_idx = words.index(br_word)
                    new_words = words[:]
                    new_words[br_idx] = r_word
                    store.append(' '.join(new_words))
``` 
3. step2 yields the set of ngrams with one move of moditification--edit1. With edit1, generate the set of ngrams with two moves of moditification. Combine edit1 and edit2 to be the set of possible corrected ngrams--candidates. 
```python
def candidates(ngram, model): 
    #"TODO: Generate possible correction"
    return set.union({ngram}, edits1(ngram, model),edits2(ngram, model))
```

4. Link to Linggle api. For all the element in candidates, compare their probabilties through Linggle. Choose the one with highest probability to be the output.
```python
def correction(ngram, model): 
#"TODO: Return most probable grammatical error correction for ngram."
    #print(candidates(ngram, model))
    #print([P(g) for g in candidates(ngram, model)])
    return max(candidates(ngram, model), key=P)
```
5. I/O of files
```python
with open('input.txt','r') as inputf, open('output.txt', 'w') as outputf:
    lines = inputf.readlines()
    
    for line in lines:
        corr_line = correction(line[:-1], model) #line[:-1]去除最後面的'\n'
        outputf.write(corr_line + '\n')
        print(corr_line)
```        

### Debug:
When I first finished the code, I observed some really weird results. Some of the outputs are of too many words. I thought there was something wrong in the impplementation of insert, but there wasn't. I temperorily
modified candidates to be only ngram + edit1. I found some outputs still had two or more edit distances. It turned out that when I assign new_words list
```python
new_words = words
```
new_words and words share the same memory, so words is modified for several times in one iteration. The code should be
```python
new_words = words[:]
```
### Reference:http://norvig.com/spell-correct.html


