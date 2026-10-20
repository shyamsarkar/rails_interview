# Writing Text to a File

```ruby
my_str = %q{
  this is
  a multiline
  string
}

File.open("example.txt", "a") do |file|
  file.puts my_str
end
```
