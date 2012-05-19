CDBSharp
========

Library for reading/writing CDB (Constant Database) files

```c#
class Program {
    static void Main(string[] args) {
	   	using (var writer = new StringCDBWriter("verses.cdb")) {
	 		  foreach (var verse in parseVerses(new StreamReader(new GZipStream(new WebClient().OpenRead("https://s3.amazonaws.com/mongol.ephisys.com/Data/kjv.rawtxt.gz"), CompressionMode.Decompress)))) {
    			writer.AddEntry(verse.Key, verse.Value);
  			}
  	  }

  	  using (var cdb = new StringCDBReader("verses.cdb")) {
  			Console.WriteLine(cdb.Find("John3:16").Single());
  	  }
    }

  private static IEnumerable<KeyValuePair<string,string>> parseVerses(StreamReader sr) {
		// Iterator to return verses as long as we keep finding more.  Loads about 31,000 verses.
	  while (!sr.EndOfStream) {
			var match = Regex.Match(sr.ReadLine(), @"(?<Reference>\d*[A-Za-z]+\d+:\d+)\s(?<Verse>.+$)");
		  if (match.Success) {
				yield return new KeyValuePair<string, string>(match.Groups["Reference"].Value, match.Groups["Verse"].Value);
		  }
	  }
  }
}
```