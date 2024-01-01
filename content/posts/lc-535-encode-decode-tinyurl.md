---
title: "Coding Solutions (Java): LeetCode 535 - Encode and Decode TinyURL"
date: 2024-01-01T23:25:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 535"
tags: [code, problem solving, leetcode]
---
[LeetCode 535](https://leetcode.com/problems/encode-and-decode-tinyurl/)

Difficulty: **Medium**

Pretty straighforward solution, to encode the URL just hash it and store in it a `Hashmap`. To decode, retrieve the hashed key from the `Hashmap`.

[Why hash functions use primes](https://computinglife.wordpress.com/2008/11/20/why-do-hash-functions-use-prime-numbers/)

```java
public class Codec {
    Map<Integer, String> map = new HashMap<>();
    
    // Encodes a URL to a shortened URL.
    public String encode(String longUrl) {
        String tokens[] = longUrl.split("/");
        int hash = 0;
        for(String token: tokens) {
            hash += hash(token);
        }
        map.put(hash, longUrl);
        return "https://smallUrl/" + hash; 
    }

    // Decodes a shortened URL to its original URL.
    public String decode(String shortUrl) {
        String tokens[] = shortUrl.split("/");
        
        return map.get(Integer.parseInt(tokens[3]));
    }
    
	// Hashes a token to a unique value.
    private int hash(String s) {
        int hash = 7;
        for (int i = 0; i < s.length(); i++) {
            hash = hash * 31 + s.charAt(i);
        }
        
        return hash;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.decode(codec.encode(url));

```