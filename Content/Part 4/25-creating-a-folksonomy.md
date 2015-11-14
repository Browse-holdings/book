

## 25. Creating a Folksonomy

In the wake of the exodus to Habari, the project began to evolve. In March 2007, Robin Adrianse ([rob1n](https://profiles.wordpress.org/rob1n)) became WordPress’ first temporary committer when he received commit access for three months to help Ryan Boren address languishing trac tickets. 

Also in March, the plugin directory launched. Before the plugin directory, developers hosted their plugins on their website. The directory gave them exposure to a huge number of WordPress users. Samuel Wood ([Otto42](http://profiles.wordpress.org/otto42)) [recalls how the plugin directory encouraged him to distribute his code](http://archive.wordpress.org/interviews/2014_06_07_Wood.html#L204). “I was writing them before, but I didn't give them to anybody. It encouraged me to release plugins because I had a place to put them.”	

<img src="../../Resources/images/25/plugin-directory-2007.jpg" width="800px" />

*The WordPress Plugin Directory in 2007*

WordPress 2.1 had launched in January 2007, after a release cycle of more than a year. WordPress 2.2 was the first to adopt the new 120-day release cycle. This goal, which would later become codified as “deadlines are not arbitrary” in WordPress’ [philosophy](https://wordpress.org/about/philosophy/), was an ongoing challenge. it was put to the test even in WordPress 2.2, which featured a new taxonomy system -- the biggest database architecture changes to date. Developing in an open source environment means leaving time for every voice to be heard, waiting for volunteers with busy lives to get things done, and discussing new features and architectural changes. It’s a challenge that WordPress would have to address release cycle after release cycle.

In the early 2000s, the internet abounded with discussions about the best way to organize information. Content has metadata assigned to it, which can be used to organize and display information. Traditional web classification methods imposed a top-down categorization structure. A website created a category structure and users placed their content in the correct category. These structures were often rigid, forcing users to shoehorn their content into something that didn’t necessarily fit. A new method of classification emerged -- tags, a bottom-up classification form in which an index or a cloud can be generated based on keywords that the creator applies to the content.	

Social bookmarking site [Del.icio.us](https://delicious.com/) was the first to use tags. While Del.icio.us wasn't a bookmark management pioneer, its tagging system set it apart. Users tag the links they save, and the tags are then used to group together links in a user’s own collection and across the entire social network. Visiting the link http://delicious.com/tag/php displays all links tagged PHP.	

Controlled, top-down content classification methods failed to scale on Web 2.0 applications in which millions of users created and organized their content. Users couldn’t be forced to use controlled vocabularies. [Clay Shirky discussed the problem](http://many.corante.com/archives/2005/01/07/folksonomies_controlled_vocabularies.php):

> users pollute controlled vocabularies, either because they misapply the words, or stretch them to uses the designers never imagined, or because the designers say “Oh, let’s throw in an ‘Other’ category, as a fail-safe” which then balloons so far out of control that most of what gets filed gets filed in the junk drawer. Usenet blew up in exactly this fashion, where the 7 top-level controlled categories were extended to include an 8th, the ‘alt.’ hierarchy, which exploded and came to dwarf the entire, sanctioned corpus of groups.	

The classification system in which users classify content themselves, creating mass tagging networks, became known as a [folksonomy](https://en.wikipedia.org/wiki/Folksonomy).

In 2005, Technorati, the blog search engine, [launched its own tagging system](http://www.sifry.com/alerts/archives/000270.html). This created a tag search across major platforms such as Blogger and Typepad, CMSs like Drupal, and other services such as Flickr, Del.icio.us, and Socialtext. 	

WordPress lagged behind, and there was pressure from both the free software and WordPress.com communities to add tags to the platform. WordPress’ native classification form is hierarchical, top-down categories. To interact with WordPress, [Technorati picked up the “tag” from the WordPress post’s category](http://lorelle.wordpress.com/2005/09/11/adding-technorati-tags-to-wordpressmu-sites/#comment-113). Many users found this unsatisfactory, as categories and tags are two different types of classification.

On his blog, [Carthik Sharma wrote](http://carthik.net/blog/vault/2006/02/21/tags-are-not-categories/):	

> Categories can be tags but tags cannot be categories. Categories are like the huge signs you see on aisles in supermarkets – “Food”, “Hygiene”, “Frozen,” etc., they guide you to sections where you can find what you are looking for. Tags are like the labels on the products themselves.	

Categories and tags address two distinct use cases. Categories are more rigid, whereas tags are a lightweight way of classifying content. There was, of course, a plugin that did the job: WordPress users installed the [Ultimate Tag Warrior](http://neato.co.nz/ultimate-tag-warrior/) plugin (though WordPress.com users could not).	

In 2007, [Ryan Boren opened a ticket to add tagging support to WordPress](https://core.trac.wordpress.org/ticket/3723). Finally, WordPress would have tags. The next step was to identify the database schema.

A database stores all the content and data of a WordPress user. The database contains different tables from which data is retrieved. There is a post table, for example, which stores post-related data. The user table stores user data. Making changes to the database is not trivial. Any changes have to be done correctly because undoing them in the future can be difficult. Changes need to be performant. In a PHP/MySQL setup, increasing the number of database queries can slow the site down. The question arose: where should we store tagging data? Should it be in a new table? Or should it be stored in an existing table?
	
Ryan proposed two database schemas. One of these [created a new table for tags](https://core.trac.wordpress.org/attachment/ticket/3723/tagging.diff). Matt was keen on the second proposal -- putting [tags in the categories database table](https://core.trac.wordpress.org/attachment/ticket/3723/tags.diff). He believed it didn’t make sense to create another table identical to the categories table. In a [comment on Trac he wrote](https://core.trac.wordpress.org/ticket/3723#comment:16):

> We already have a ton of rewrite, API, etc. infrastructure around categories. Mostly I see tagging as a new interface to the data. On the display side, people want their tags listed separately from their categories, and probably something like a tag cloud function.	

WordPress had already successfully reused tables in the past; for example, posts, pages, and attachments are all stored in the same table, and at that time the categories table also contained link categories. Tagging, from a user’s perspective, enabled them to tag posts, display a list of tags, and display posts with the same tag. What was the point in duplicating the infrastructure when it could be achieved within the current table system?	

[Few developers supported](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/011730.html) putting tags in the categories table. Some believed the table would become bloated and argued that adding tags to it meant including additional code to keep the two taxonomy types separate. This additional code could introduce bugs and make future maintenance and extension of the table difficult for developers.
	
On [wp-hackers](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/thread.html), April 2007 was spent discussing the new database schema for taxonomies. A suggestion that gained considerable community traction was splitting categories, link categories, and tags into their own individual tables -- but it was impossible to reach consensus. 

After checking in the single-table taxonomy structure (changesets [#5110](https://core.trac.wordpress.org/changeset/5110) and [#5111](https://core.trac.wordpress.org/changeset/5111)), Matt posted a new thread on wp-hackers [making the case for using the categories table](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/011930.html). He argued that: 

1. It would perform faster as no additional queries would need to be carried out to support tags. A separate tag table would require at least two extra queries on the front end. 
2. It would provide a better long-term foundation. Tags and categories would be able to share terms (for example the category “dogs” and the tag “dogs”). 
3. There were no user-facing or plugin-facing problems.	

In the thread, he also proposed an alternative, inspired by Drupal’s taxonomy system: creating a new table for terms within a specific taxonomy. Terms are the items within a category, tag, or any other taxonomy; dog, cat, and chicken are all terms within an “animal” taxonomy. This additional table allowed terms to be shared among taxonomies while having the same ID (the ID is what identifies an item in the database). Just one term -- “dog” for example -- would be saved in the database, and this term could be used in any taxonomy. 

Ryan Boren [proposed a compromise](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/011991.html), one which enabled individual terms to be part of any taxonomy while keeping the same ID. It was a three-table solution, with tables for terms, taxonomies, and objects. Discussion ensued, a [new Trac ticket was opened up](https://core.trac.wordpress.org/ticket/4189), and a new structure was created based on this proposal. The first table, wp_terms, holds basic information about single terms. The wp_term_taxonomy table places the term in a taxonomy. The final table, term_relationships, relates objects (such as posts or links) to a term_taxonomy_id from the term_taxonomy table.

<img src="../../Resources/images/25/taxonomy_structure.jpg" alt="an image showing the current taxonomy structure of WordPress" />

*The database structure for WordPress taxonomies*

This approach had the advantage of assigning one ID to a term name while using another table to relate it to a specific taxonomy. It's extensible for plugin developers who can create their own taxonomies. It also enables large multisite networks, such as WordPress.com, to create global taxonomies -- unified tagging systems in which users of different blogs can share terms within a taxonomy.

Like many WordPress features, tags landed on WordPress.com before they shipped in WordPress. From the beginning, the new structure caused huge technical problems. The increase in tables meant that WordPres.com needed more servers to deal with the additional queries. “It was slower to be completely honest,” [says Matt](http://archive.wordpress.org/interviews/2014_07_07_Mullenweg.html#L156). “That was a cost that we saw in a very real way on WordPress.com, but also a hidden cost that we did impose on everyone who was doing WordPress in the world.”	

More than just a challenging code problem, the taxonomy implementation highlighted problems in the development process. Heavy discussion meant development dragged on. Some wanted to delay the release. Some wanted to pull the feature. Others wanted to revise the schema in a subsequent version. Andy Skelton [responded to the wp-hackers discussion](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/011988.html):

> To include a premature feature in an on-time release degrades the quality of the product. I refer to not only the code but the state of the community. Increments are supposed to be in the direction of better, not merely more. Better would be to release on time with a modest set of stable upgrades.	
						
> To block release while the one new feature gets sorted out would be a maladjustment of priorities. If 2.2 seems light on sex appeal, so be it. Better to keep the release date as promised.	

The 120-day release schedule was in danger because of one issue. Making major architectural changes to core just weeks before a release was contrary to the aims of the new development process. A thread suggested [delaying 2.2's release](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/011901.html). The architectural changes were too important to be done in an unsatisfactory way.

Eventually, Matt decided to [delay the 2.2 release](http://lists.wordpress.org/pipermail/wp-hackers/2007-April/012090.html), pull tags out of core, and bring in widgets -- a user-facing feature that would encourage people to update their version of WordPress.

[Andy Skelton developed widgets](http://andy.wordpress.com/2006/03/08/widgets-user-interface-and-api/) for WordPress.com users; he built them to give bloggers more flexibility with their site's layout. Widgets are code blocks users can drag and drop into place through the user interface. They allow bloggers to add a calendar, a search bar, or some text (among other things) to a sidebar in any order they wish. It was a hugely popular feature on WordPress.com; the plugin for WordPress was also a great success. Launching widgets on WordPress.com meant that many different users could test them before they made it into core as a feature. 

The new tagging feature finally shipped in WordPress 2.3 -- in a structure that was the outcome of extensive negotiating and haggling. This wrangling process has had consequences for WordPress developers ever since. Shared terms have had lasting implications for core developers, plugin developers, and users. The problem with shared terms is that an item could have multiple different meanings, but the database treats all of them identically. For example, the word apple. A developer creates the taxonomy “Companies” and the taxonomy “Fruit” and the user places the term “apple” in both. Conceptually, this is a different item -- a company and a fruit -- and they appear in the user interface as two distinct entities. But the database treats them as the same thing. So if a user makes changes to one -- for example, capitalizing it -- changes are made to both.

In a post in 2013, [Andrew Nacin wrote that](http://make.wordpress.org/core/2013/07/28/potential-roadmap-for-taxonomy-meta-and-post-relationships/) “Hindsight is 20/20, and shared terms are the bane of taxonomies in WordPress.” Each term is represented in two different ways. Since an individual term can appear in multiple taxonomies, it’s not straightforward to identify the actual term in the actual taxonomy that you want. It has become a challenge to build new features that use one identification method when there are parts of WordPress that use the other.

A case in point: to attach metadata to a term there must be one object identifier. However, the public ID uses a different identifier. It is extremely difficult to target data consistently when it is identified in multiple ways. WordPress has a long-term commitment to maintaining backward compatibility, so creating a new schema isn’t possible. The effort to get rid of shared terms must progress step by step, over a number of major releases (this is being carried out over three releases in the 4.x series).

The over-engineered taxonomy system came about through argument and compromise. When the bazaar model works, it can produce software that people love to use. The model fails when intractable arguments result in compromises that no one is 100 percent happy with. The new taxonomy system did, however, contain one notable benefit. Neither of the original proposals for the taxonomy schema (putting tags into the category table, or creating a new table just for tags) would have allowed developers to create custom taxonomies, and the latter became a major element in WordPress' transition from a straightforward blogging platform to a bonafide content management system (CMS). 

Even with these setbacks, software development continued mostly on schedule. Since tags were pulled from 2.2, there were only 114 days between WordPress 2.1 and 2.2, and then 129 days between the release of 2.2 and 2.3. While delays would recur in some future releases, there was nothing that resembled the long dark year between 2.0 and 2.1.	
