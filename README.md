# wikisearch

This project is a simple search program that searches for a user's input in a corpus of wikipedia documents.

The main objects are Index and Query. 

The index program takes the name of the wikipedia corpus file, the name of a file to write all the titles of the articles to, the name of the file to write information about every word in the corpus, the name of a file to write information about all the documents.
For example:
scala search.Index MedWiki.xml titleFileMed.txt wordFile.txt docFileMed.txt

The Query program takes three mandatory arguments; first the name of the title file written by the indexer, second the name of the document file written by indexer, and third the name of the word file written by the indexer. It also takes the optional argument —knight-rank which should be given as the first argument. If —knight-Rank is called then the process of ranking the documents will be employed when determining which documents to show first in the search. 
For example run:
scala search.Query —knight-Rank titleFileMed.txt docFileMed.txt wordFile.txt
The user will then be prompted to enter a search phrase. 
The program will then output the top 10 documents corresponding to the search phrase. 


The program first uses Index to index a wikepedia corpus. Index first parses the XML.
To do this it gets all pages in the XML file. It adds the page's titles and ids to 
a hash table of titles and ids. Then it goes through the text in the page. It stems the text, using the PorterStemmer class. It seperates links to other documents, keeping track of each document's outlinks. Then it adds all the words to a hashtable containing all the words in the corpus. This hash table contains words as keys, and the value is a tuple of the number of documents the word occurs in and a hash map of these document ids and the frequency of that word in that document. Once all the words have been added to the table, the euclidian distance of each document is calculated by iterating through the word table.
After all the xml has been parsed and all the document's outlinks have
been found, knight rank is added to the document information hashtable using the KnightRanker class. The KnightRanker class takes in a threshold  value of when to stop iterating, the title to id hash map, and the links out from each page. GetKnightRanks first initializes the link matrix A. Then, it calculates a transformation matrix, M, so that M = (1-dampeningFactor)A + dS, where S is 1/numberPages for each entry. It continually transforms a vector (initially 1/n for all entries) until the difference
between vector length in iterations is less than the threshold value.
Then the Index class writes out files form its hashtable. By iterating through the titleToID hash table the index class writes titleFile containing docId's and titles. 
By iterating through the docInfo hashtable the Index class writes a docFile containing the 
docId followed by a documents knight rank and then its euclidian normalization.
By iterating through the wordTable the Index class writes a file containing every word in the corpus followed by its inverse document frequency. Followed by a word and its idfw is a list of all the docId's, and the number of times that word appears in the document.

The Index class prepares these files for the Query class.
The querier takes in optional commands --smart (which does nothing currently), --knight-rank, which scores a documents using their knight rank as well as their document score. 
The query class also takes in the title file, document info file, and word file.
The query program first builds a hashtable of info about each word. The wordTable contains
every word in the corpus as its key. The value is a tuple containing the idfw and a map
of docIds and frequency of the word in that doc. 
The query then builds the docInfo table. The key is the document id and the value is a tuple containing the name of the doucment, the knight rank, and its euclidian norm. 
After the two hash tables are built, the query program can answer user queries. To do this the query program enters a loop reading user input from the terminal. Until the user types ":quit" the query class will answer the query in the following way. First it stems and decapitalizes the query. Then it forms a set of all document containing the word. Then it calculates the score of all these documents,putting them in a priority queue. It then deques the top ten document and displays answers to a user.


Testing

For implementing Knight Rank the program ran unit tests. The vector and matrix
classes both contain unit tests. The KnightRanker class contains an example of knight ranking done on a simple example of 4 webpages with links to various pages. 

I tested the indexer on small xml files. I manually counted the number of words in documents and ensured that the indexer was outputing the correct results in the files it wrote to.
See test4Documentation.txt for an example of how I went about ensuring that the indexing was working correctly. 

To test the querier U printed the scores, and knight ranks of the web pages for specific queries. 
Examples of this are shown in testing.txt. I also made sure that the Query program seems to  output logical web pages for various queries. 

Documentation of testing on MedWiki is in testing.txt. Documentation of testing on 
BigWiki is in testingBig.txt

