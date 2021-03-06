---
layout: post
title: "Lightweight Set of Boolean ওরফে Bitmask"
date: 2012-12-20 12:00:00
permalink: /lightweight-set-of-boolean-bitmask.html
---
তোমাদের জানা আছে কম্পিউটারে integer কিছু বিটের সমন্বয়ে স্টোর হয়ে থাকে। আমরা যেমন Boolean অ্যারে ব্যবহার করি বিভিন্ন কাজে, তেমন আমরা একটা integer এর বিটগুলা নিয়ে এই একই কাজ করতে পারি। এটাকে এজন্য বলা হয় Lightweight small Set of Boolean. তবে যখন অ্যারে সাইজ কম থাকে অর্থাৎ ১৬ অথবা ৩২ এর মধ্যে থাকে তখনই আমরা bitwise operation এর সাহায্যে অনেক সহজে এবং efficient way তে integer কে বুলিয়ান অ্যারের মত ব্যবহার করতে পারি । আমরা আজ কিভাবে এই বিটগুলার তথ্য ব্যবহার করে বিভিন্ন মজার মজার অপারেশন করা যায় তা দেখবো। প্রথমে তোমাদের মনে করিয়ে দিলাম যদিও জানা আছে সবার, বুলিয়ান অপারেশনের জন্য `&` মানে গুণ করা, এবং `|` এর অর্থ যোগ করা। (আগেই বলে রাখা উচিৎ এই টিউটোরিয়ালটি পড়ার আগে বুলিয়ান অপারেশনগুলা অর্থাৎ AND, OR, XOR, NOR, এবং bit shifting সম্পর্কে একটু জেনে রাখলে বুঝতে সুবিধা হবে)

1. প্রথমে দেখি bitmask দিয়ে কিভাবে কোন সংখ্যাকে ২ দিয়ে গুণ/ভাগ করা যায়।
    
    ```
    S = 34 (base 10) = 100010 (base 2)
    S = S<<1 = S*2 = 68 (base 10) = 1000100 (base 2)
    S = S>>2 = S/4 = 17 (base 10) = 10001 (base 2)
    S = S>>1 = S/2 =  8 (base 10) = 1000 (base 2) //লক্ষ্য কর শেষের 1 টা নাই আর[/code]
    ```
    
2. এখন দেখি কিভাবে j-তম বিট অন করা যায়। অর্থাৎ j-তম বিটে 1 যোগ করলেই সেটা অন হয়ে যাবে। এজন্য আমাদের ব্যবহার করতে হবে S |= ( 1<< j) এটা কিভাবে কাজ করে সেটা নিচের উদাহরনটা দেখে বোঝ কিনা দেখ তো।
    
    ```
    S = 34 (base 10) = 100010 (base 2)
    j = 3, (1<<j)    = 001000 (base 2)   // j এর মান 1 ছিল। left shifting করে আমরা j এর
                                        //প্রথম বিটটা ৩ ঘর বামে সরিয়ে দিয়েছি।
                   ----------------   ( OR operation অর্থাৎ বিটে ১ যোগ হচ্ছে  )
    S = 42 (base 10) = 101010 (base 2)   // S এর মান আপডেট হয়েছে।
    ```
    
3. এখন যদি চাও j-তম বিটের তথ্য জানতে অর্থাৎ j-তম বিট 1/0 সেটা জানতে চাই। এজন্য আমরা অবশ্যই j ভেরিয়েবলটার j-তম বিটের মান 1 করে নিব। তারপর S ভেরিয়েবলটার সাথে “&” operation করবো। কেন? কারনটা নিচের উদাহরনটা দেখলেই বুঝতে পারবে আশা করি।
    
    ```
    S = 42 (base 10) =  101010 (base 2)
    j = 3, (1<<j)    =  001000 // ৩টা বিট শিফট করলাম।
                   ---------------- ( AND operation )
    T = 8 (base 10)  =  001000 // অর্থাৎ S এর ৩ নং বিটে 1 আছে।
    ```
    
    আরও একটা উদাহরন দিলাম:
    
    ```
    S = 42 (base 10)  =  101010 (base 2)
    j = 2, (1<<j)     =  000100 // 1 কে ২ বিট শিফট করলাম
                   ---------------- ( AND operation )
    T = 0 (base 10)   =  000000 (base 2) // অর্থাৎ j-তম বিটে 0 আছে।
    ```
    
4. এবার দেখি কিভাবে j-তম বিটের মান clear/turn off করতে হয়। এজন্য আমরা ব্যবহার করি, S &= ~(1<<j). bitwise NOT এর কাজ হল বিটে যে মানটি আছে তার বিপরীত করে দেয়া। তাইলে এক্ষেত্রে j ভেরিয়েবলটির j-তম পজিশনের মান 0 হবে এবং বাকি সকল বিটের মান 1 হবে। এখন যদি “&” অপারেশন চালাই তাইলে S এর j-তম বিট সর্বদা 0 হয়ে যাবে। উদাহরনটা দেখলেই বুঝবে:
    
    ```
    S = 42 (base 10) =  101010 (base 2)
    j = 1, ~(1<<j)   =  111101 // এটাই bitwise “NOT” operation.
                    ----------------.  ( AND operation )
    S = 40 (base 10) =  101000  // S এর j-তম বিটের মান আপডেট হয়েছে
    ```
    
5. এখন যদি আমরা j-তম বিটের মান toggle অর্থাৎ বিপরীতটা করতে চাইলে কি করবো? এজন্য bitwise XOR operator ব্যবহার করবো। কারন XOR এর মান তখনই 1 যখন দুইটা বিটের মান ভিন্ন হবে। একাজের জন্য আমরা ব্যবহার করবো S ^= (1<<j)
    
    ```
    S = 40 (base 10)  =  101000 (base 2)
    j = 2, (1<<j)     =  000100 (base 2) 
                    ---------------- (XOR operation. True if both bits are different)
    S = 44 (base 10)  =  101100 (base 2) // S এর j-তম বিটের মান আপডেট হয়েছে।
    ```
    
আপাতত এপর্যন্তই। কিছু বুঝতে সমস্যা হইলে কমেন্টে জানাবে অবশ্যই।
