# No One Knows Your Data Better than You (Even in Oracle 18c)  

Oracle’s cost based optimizer is an amazingly sophisticated piece of software, but still it manages to confuse us by how it handles some queries. The reason is that usually we still know more about our data than the optimizer does.
But with 18c you get the possibility to leverage these effects with the help of the pluggable optimizer which already is my favourite 18c feature. It will be available in 18.1.4.
[nbsp]
<span id="OBJ_PREFIX_DWT752_com_zimbra_date" class="Object"><span id="OBJ_PREFIX_DWT778_com_zimbra_date" class="Object">So</span></span> how does it work? You might have read about the <a href="http://www.oracle.com/technetwork/database/multilingual-engine/documentation/index.html" target="_blank" rel="noopener">Oracle Database Multilingual Engine</a> (MLE). The actual reason MLE was introduced was to enable developers to write their own pluggable optimizer cartridges (POC).
Now you can - depending on your personal preferences - create optimizers in your favourite language <em>and</em> customise them depending on your application’s specific requirements. Sounds cool, doesn’t it?

Bild

The basis you can build your optimizer cartridge on is the statement’s parse tree. You can access existing statistics and histograms, gather statistics on the fly, and combine them with different transformations. The standard transformations like <span id="OBJ_PREFIX_DWT754_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT780_com_zimbra_url" class="Object">join elimination</span></span>, <span id="OBJ_PREFIX_DWT755_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT781_com_zimbra_url" class="Object">view</span></span> <span id="OBJ_PREFIX_DWT756_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT782_com_zimbra_url" class="Object">merging</span></span>, <span id="OBJ_PREFIX_DWT757_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT783_com_zimbra_url" class="Object">subquery</span></span> <span id="OBJ_PREFIX_DWT758_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT784_com_zimbra_url" class="Object">unnesting</span></span>, <span id="OBJ_PREFIX_DWT759_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT785_com_zimbra_url" class="Object">join predicate pushdown</span></span>, and <span id="OBJ_PREFIX_DWT760_com_zimbra_url" class="Object"><span id="OBJ_PREFIX_DWT786_com_zimbra_url" class="Object">join factorization</span></span> are available via API, so you don’t have to reinvent the wheel. In case of failure the database falls back to the standard cost-based optimizer, so the application doesn’t need to handle an exception. The information about which optimizer has been used can be found in the trace file. Within PL/SQL you can access it after every SQL or DML statement with SQL%OPTIMIZER which returns a string.
[nbsp]
This is a standard explain plan:
[nbsp]

Bild

[nbsp]
And this is the plan our pluggable optimizer generated:
[nbsp]

Bild

[nbsp]
As you need special grants to plug your optimizer cartridge (<em>define optimizer</em> and <em>execute optimizer</em>) we can’t test it on <a href="https://livesql.oracle.com/apex/livesql/file/content_GAM6447L92HES1WQ8GM8INEST.html" target="_blank" rel="noopener">LiveSQL </a>(yet?) but as soon as 18c will be available for download we can start coding and testing.
If you want to view examples and share your best optimizer cartridges, go to <a href="https://github.com/its-people/Optimizer-Cartridge-Exchange" target="_blank" rel="noopener">Optimizer Cartridge Exchange</a>.
Looking forward to your ideas!
[nbsp]
[nbsp]
