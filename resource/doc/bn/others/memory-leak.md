# মেমোরি লিক সম্পর্কে
ওয়েবম্যান একটি স্থিতিশীল মেমোরি ফ্রেমওয়ার্ক, তাই আমাদের এখানে মেমোরি লিকের অবস্থা একটু লক্ষ্য দেওয়া দরকার। তবে, উন্নত অঙ্গেরা চিন্তা করার প্রয়োজন নেই, কারণ মেমোরি লিক খুব সময়ের মধ্যে ঘটে এবং সহজে দুর করা যায়। ওয়েবম্যান ডেভেলপমেন্ট এবং ট্রাডিশনাল ফ্রেমওয়ার্ক ডেভেলপমেন্ট অভিজ্ঞতা অনুসারে মূলত একই আচরণে কাজ করে, মেমোরি ব্যবস্থাপনা নিয়ে অতিরিক্ত দৈবচিন্তা করতে হয় না।

> **পরামর্শ**
> ওয়েবম্যান এসোসিয়েটেড মনিটর প্রসেস সমস্ত প্রসেসের মেমোরি ব্যবহারের অবস্থা মনিটর করে, যদি প্রসেসের মেমোরি অনুমোদিত php.ini এর 'memory_limit' এর মান উপযুক্ত হতে যায়, তাহলে স্বয়ংক্রিয়ভাবে প্রসেস পুনরায় শুরু করা হবে, প্রসেসের মেমোরি মুক্ত করার অবস্থান প্রাপ্ত হবে, এই সময়ে ব্যবসার কোনো প্রভাব হবে না।

## মেমোরি লিক সংজ্ঞা
অনুরোধের অনুরূপতার সাথে ওয়েবম্যান অধিগ্রহণ করে মেমোরির ব্যবহারকারীদের জন্য অসীমভাবে বাড়তি, এমনকি কয়েক শ’ এর হার্ডতে কড়ি হয়ে যাবে, এটা মেমোরি লিকের একধরণ। এটা একধরণের হয়।

প্রসেস যখন অনুরোধ গ্রুপগুলির সাথে সংশ্লিষ্ট, এনক্রিপ্ট অথবা কাজ না করে। আবার পরবর্তীতে বৃদ্ধি পায় না। তখন এই ধরণের গণনা করা হয় না।

সাধারণভাবে, প্রসেসের একটি পরিষ্কার খাতের মাধ্যমে একই ধরণের নিয়মিতভাবে বড় অনুরোধ বা বিশাল অনুরোধও সহজলভ্য। একই অনুরোধের পরে পিএইচপি প্রাপ্ত বিশাল অনুরোধ বা সমস্ত অনুরোধ মুক্ত না করে একাধিক কারণে। এটি স্বাভাবিক প্রসঙ্গ। (gc_mem_caches() মেথডটি ব্যাখ্যা করতে পারে অনেকগুলি মুক্ত পুনর্বিন্যস্ত মেমোরি)

## মেমোরি লিক কিভাবে ঘটে
মেমোরি লিক হবার সাথে **অবশ্যই** নিম্নলিখিত দুইটি শর্ত পূরণ করতে হয়:

1. একটি **দীর্ঘস্থায়ী** অ্যারে উপস্থিত থাকা (দীর্ঘস্থায়ী অ্যারের অনুপ্রাণিত নয়, সাধারণ অ্যারে কোনো সমস্যা নেই)
2. এবং এই **দীর্ঘস্থায়ী** অ্যারেটি অসীমভাবে বড় হওয়া (ব্যবসায়ে অনুপ্রাণিত ডাটার উদ্বৃত্তি, কোনো ডাটা মুছে দেওয়া না)

যদি 1 2 শর্ত **সময়ে** পূরণ হয় (এখানে সময়ে পূরণ হয়ে উৎপন্ন হবে), তবে মেমোরি লিক উৎপন্ন হবে। অপরিভাজিত শর্ত পূরণ করা না হলে বা একটি শর্ত পূরণ না হলে মেমোরি লিক হবে না।

## দীর্ঘস্থায়ী অ্যারে

ওয়েবম্যানে দীর্ঘস্থায়ী অ্যারেগুলি নিম্নরূপ:

1. static কীওয়ার্ডের অ্যারে
2. একটি সিঙ্গেলটন অ্যারের স্বত্ব
3. global কীওয়ার্ডের অ্যারে

> **লক্ষ্য করুন**
> ওয়েবম্যানে দীর্ঘস্থায়ী ডেটাগুলি ব্যবহার করা অনুমোদিত, তবে আপনাকে নিশ্চিত করতে হবে যে উপায়ে আপনার ডাটা সীমিত এবং অসীমভাবে বড় নয়।

নীচের উদাহরণগুলি আলাপ করা হয়

## অসীমভাবে static অ্যারে
```php
class Foo
{
    public static $data = [];
    public function index(Request $request)
    {
        self::$data[] = time();
        return response('hello');
    }
}
```

এখানে `static` কীওয়ার্ড দিয়ে ডিফাইন করা `$data` অ্যারেটি দীর্ঘস্থায়ী অ্যারে, এবং উদাহরণের মধ্যে `$data` অ্যারেটি অনুরোধ প্রতি অনুরোধ বিস্তার পায়, এর ফলে মেমোরি লিকের অবস্থা উত্পন্ন হয়।
## অসীম বাড়বার্তা সিঙ্গেলটন অ্যারে প্রপার্টি
```php
class Cache
{
    protected static $instance;
    public $data = [];
    
    public function instance()
    {
        if (!self::$instance) {
            self::$instance = new self;
        }
        return self::$instance;
    }
    
    public function set($key, $value)
    {
        $this->data[$key] = $value;
    }
}
```

কল কোড
```php
class Foo
{
    public function index(Request $request)
    {
        Cache::instance()->set(time(), time());
        return response('hello');
    }
}
```

`Cache::instance()` এনক্ষন একটি ক্যাশে সিঙ্গেলটন রিটার্ন করে এটি একটি দীর্ঘস্থায়ী অবস্থানের ক্লাস ইনস্ট্যান্স। যদিও এর `$data` প্রোপার্টিতে `static` কি ব্যবহার না করা হয়েছে, তবে ক্লাস ইন্সট্যান্স নিরাপদ, অতএব `$data` এটি দীর্ঘস্থায়ী এবং এটি অসীম বাড়তে যাচ্ছে। বিভিন্ন কীতে ডেটা যোগ করা হলেও, প্রোগ্রামটি অধিক মেমোরি জবটা কেতেছে, যেটা মেমোরি লিক তৈরি করছে।

> **লক্ষ্য করুন**
> যদি `Cache::instance()->set(কী, মান)` এড কিওয়ার্ড দেওয়া হয়, তবে মেমোরি লিক ঘটেনা, কারণ `$data` অ্যারে অসীম বাড়বার্তা নাই।

## অসীম বাড়বার্তা গ্লোবাল অ্যারে
```php
class Index
{
    public function index(Request $request)
    {
        global $data;
        $data[] = time();
        return response($foo->sayHello());
    }
}
```
ফাংশন বা ক্লাস মেথড সমাপ্তির পরে গ্লোবাল কীওয়ার্ডে ডিফাইন করা হওয়া অ্যারেগুলি উদ্দারণ করেননা, তাই এটি দীর্ঘস্থায়ী অ্যারে, উপরোক্ত কোডটির মতো উপরোক্ত নিয়মেই মেমোরি লিক সৃষ্টি করবে। এভাবে একই গবেষণার ম্ধয়মে ফাংশন বা মেথডে `static` কীওয়ার্ডে ডিফাইন করা অর্থ স্থায়ী অ্যারেগুলিও দীর্ঘস্থায়ী, যদি অ্যারেটি অনুসীমতা পূর্ণ হয়, তবে মেমোরি লিক ঘটতে পারে, উদাহরণস্বরূপঃ
```php
class Index
{
    public function index(Request $request)
    {
        static $data = [];
        $data[] = time();
        return response($foo->sayHello());
    }
}
```

## পরামর্শ
ডেভেলপারদের পরামর্শ দেওয়া যায় যে, মেমোরি লিক ব্যুৎপত্তি প্রাচীর নয়, কারণ এটি খুব বেশী ঘটেনা, যদি দুর্দান্ত ঘটে তাহলে আমরা টেস্টিং দিয়ে জেনে নিতে পারি কোন কোডে লিক ঘটে এবং সমস্যা সনাক্ত করতে। আপনি যদি না পারেন, তাহলে webman এর নিজস্ব মনিটরিং সেবা সময়ে সময়ে সুরক্ষিত নবায়ন করে অ্যাপলিকেশনের মেমোরি লিক হওয়া প্রক্রিয়াকে মুক্ত করে।

যদি আপনি কল্পনা করেন যে আমন্ড অতীতে মেমোরি লিক হওয়ার সম্ভাবনাই দ্বারা নিষিদ্ধ করা যায়, তাহলে নিম্নোক্ত পরামর্শ দেওয়া হয়।
1. গ্লোবাল, static মুখস্থী কীউয়ার্ড দিয়ে অ্যারে ব্যবহার করা হবে না, যদি ব্যবহার করতে হয়, তাই নিশ্চিত করা উচিত যে সেগুলি অসীমভাবে বাড়তে না।
2. অজানা ক্লাসের জন্য, সিঙ্গেলটন ব্যবহার না করা হবে, `new` কীওয়ার্ড দিয়ে তাদের আরম্ভ করা হবে। যদি সিঙ্গেলটন দরকার হয়, তাহলে তারা যে অসীমভাবে বাড়বার্তা অ্যারে আছে, সেগুলিকে পরীক্ষা করা হবে।