## Q. How do you read a 20GB log file?

Read the file in small chunks instead of loading the entire file into memory. Ruby reads at most the requested number of bytes, processes that chunk, and then continues from the current file position.

```ruby
File.open("log.txt") do |f|
  while chunk = f.read(8192)
    puts chunk
  end
end
```

The loop ends when `read` returns `nil`, meaning the end of the file was reached. Each chunk is up to 8,192 bytes; the final chunk may be smaller. A chunk can also split a line, so this approach is best when processing raw data rather than complete lines.

```text
Disk -> [8192 bytes] -> process (may split a line)
     -> [8192 bytes] -> process
     -> [8192 bytes] -> process
```

If the file may contain invalid or unknown byte sequences, open it in binary mode and explicitly convert each chunk to UTF-8:

```ruby
File.open("log.txt", "rb") do |f|
  while chunk = f.read(10000)
    puts chunk.encode("UTF-8", invalid: :replace, undef: :replace, replace: "?")
  end
end
```

- `"rb"` reads the file as raw binary bytes.
- `encode` converts the bytes to UTF-8.
- Invalid or undefined characters are replaced with `?` instead of raising an error.

For line-by-line processing, use `File.foreach("log.txt")`, which also avoids loading the whole file into memory.