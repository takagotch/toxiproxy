### toxiproxy
---
https://github.com/shopify/toxiproxy


```rb
Toxiproxy[:mysql_master].downstream(:latency, latency: 1000).apply do
  Shop.first
end

Toxiproxy[/redis/].down do
  Shop.first
end

class Post < ActiveRecord::Base
  
  def tags
    TagRedis.smembers(tag-key)
  end
  
  def add_tag(tag)
    TagRedis.sadd(tag_key, tag)
  end
  
  def remove_tag(tag)
    TagRedis.rem(tag_key, tag)
  end
  
  def tag_key
    "post:tags:#{self.id}"
  end
end

require 'toproxy'

Toxiproxy.populate([
  {
    name: "toxiproxy_test_redis_tags",
    listen: "127.0.0.1:22222",
    upstream: "127.0.0.1:6379"
  }
])

TagRedis = Redis.new(port: 22222)

test "should return empty array when redis is down listing tags" do
  @post.add_tag "mammals"
  
  Toxiproxy[/redis/].down do
    assert_equal [], @post.tags
  end
end

def tags
  TagRedis.smembers(tag_key)
rescue Redis::CannotConnectError
  []
end


Toxiproxy.populate([
  {
    name: "shopify_test_redis_master",
    listen: "127.0.0.1:2220",
    upstream: "127.0.0.1:6379"
  },
  {
    name: "shopify_test_mysql_master",
    listen: "127.0.0.1:24220",
    upstream: "127.0.0.1:3306"
  }
])


redis = Redis.new(port: 6380)
redis = Redis.new(port: 22220)

redis = Redis.new(port: 2220)

Toxiproxy[:shopify_test_redis_master].downstream(:latency, latency: 1000).apply do
  redis.get("test")
end

```

```
wget -O toxiproxy-2.1.4.deb htttps://github.com/Shopify/toxiproxy/release/download/v2.1.4/toxiproxy_2.1.4_amd64.deb
sudo dpkg -i toxiproxy-2.1.4.deb
sudo service toxiproxy start

brew tap shopify/shopify
brew install toxiproxy

docker pull shopify/toxiproxy
docker run -it shopify/toxiproxy

make build
./toxiproxy-server

toxiproxy-cli create shopify_test_redis_master -l localhost:2679 -u localhost:6379

toxiproxy-cli toxic add shopify_test_redis_master -t latency -a latency=1000

toxiproxy-cli create redis -l localhst:26379 -u localhost: 6379
toxiproxy-cli list

redis-cli -p 26379
toxiproxy-cli toxic add redis -t latency -a latency=1000
redis-cli -p 26379
toxiproxy-cli toxic remove redis -n latency_downstream
redis-cli -p 26379
toxiproxy-cli delete redis
redis-cli -p 26379
```

```
```


