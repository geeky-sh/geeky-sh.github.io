---
layout: page
title: Downloading Large files in ruby 
---

# Downloading large files in ruby

When you are downloading large files using `net/http` or `Faraday`, you can't just do:

```ruby
Faraday.get(url) 

## or

uri = URI.parse("http://google.com/")
response = Net::HTTP.get_response(uri)

```

This will bloat up the memory and your system will become unresponsive. In order the mitigate this problem, we need to write a function that downloads the file in segments, and ensure that it doesn't keep the downloaded data in memory until the entire download is complete.
Here is the function that I have written to accomplish that:

```ruby
def file(url)
  ## to get the file name that is being downloaded
  filename = url.split('/').last.split('?').first
  file = File.open(File.join('/tmp', "##{filename}"), 'wb')

  parsed_uri = URI.parse(url)
  Net::HTTP.start(parsed_uri.host) do |http|
    request = Net::HTTP::Get.new parsed_uri.request_uri
    begin
      http.request(request) do |resp|
        resp.read_body do |segment|
          file.write(segment)
        end
      end
    ensure
      file.close
    end
  end

  file
end
```

Using this function, it helped me to download very large file in chunks and writing those chunks instead of whole data