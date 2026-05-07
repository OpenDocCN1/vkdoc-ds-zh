![](Images/A978-1-4842-0259-3_CoverFigure_HTML.jpg)

![A978-1-4842-0259-3_CoverFigure_HTML.jpg](Images/A978-1-4842-0259-3_CoverFigure_HTML.jpg)Jerry Rackley Marketing Analytics RoadmapMethods, Metrics, and Tools

![A978-1-4842-0259-3_BookFrontmatter_Figa_HTML.png](Images/A978-1-4842-0259-3_BookFrontmatter_Figa_HTML.png)

Any source code or other supplementary materials referenced by the author in this text is available to readers at [`www.apress.com`](http://www.apress.com/) . For detailed information about how to locate your book’s source code, go to [`www.apress.com/source-code/`](http://www.apress.com/source-code/) . ISBN 978-1-4842-0260-9e-ISBN 978-1-4842-0259-3 DOI 10.1007/978-1-4842-0259-3 © Apress 2015 Marketing Analytics Roadmap: Methods, Metrics, and Tools Managing Director: Welmoed Spahr Acquisitions Editor: Susan McDermott Developmental Editor: Douglas Pundick Editorial Board: Steve Anglin, Mark Beckner, Gary Cornell, Louise Corrigan, James DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Susan McDermott, Matthew Moodie, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss Coordinating Editor: Rita Fernando Copy Editor: Laura Poole Compositor: SPi Global Indexer: SPi Global Cover Designer: Friedhelm Steinen-Broo For information on translations, please e-mail `rights@apress.com`, or visit [`www.apress.com`](http://www.apress.com/) . Apress and friends of ED books may be purchased in bulk for academic, corporate, or promotional use. eBook versions and licenses are also available for most titles. For more information, reference our Special Bulk Sales–eBook Licensing web page at [`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales) . This work is subject to copyright. All rights are reserved by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. Exempted from this legal reservation are brief excerpts in connection with reviews or scholarly analysis or material supplied specifically for the purpose of being entered and executed on a computer system, for exclusive use by the purchaser of the work. Duplication of this publication or parts thereof is permitted only under the provisions of the Copyright Law of the Publisher's location, in its current version, and permission for use must always be obtained from Springer. Permissions for use may be obtained through RightsLink at the Copyright Clearance Center. Violations are liable to prosecution under the respective Copyright Law. Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image we use the names, logos, and images only in an editorial fashion and to the benefit of the trademark owner, with no intention of infringement of the trademark. The use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identified as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights. While the advice and information in this book are believed to be true and accurate at the date of publication, neither the authors nor the editors nor the publisher can accept any legal responsibility for any errors or omissions that may be made. The publisher makes no warranty, express or implied, with respect to the material contained herein. Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com. Apress Media, LLC is a California LLC and the sole member (owner) is Springer Science + Business Media Finance Inc (SSBM Finance Inc). SSBM Finance Inc is a Delaware corporation. To Lisa: my wife of three decades. A man could want no finer or truer partner in life. And, To marketers who are searching for a better way to answer the question: “What have you done for us lately?” Introduction

I entered the marketing profession quite by accident. Over 30 years ago I graduated from university with a degree in computer science, and for the 4 years I was earning that degree, I worked at the university data center. I had the academic underpinnings to develop software and a résumé that included highly relevant experience. Despite the relatively poor job market when I graduated, I had three good offers. Surprising myself, I chose the one with the lowest starting salary, at IBM, because I liked the culture and upward mobility there. IBM didn’t see me as a software programmer, but instead put me in a sales and marketing branch office. I never wrote a line of code from that day forward, and I didn’t regret it.

I was immediately drawn into the world of sales and marketing, and I learned everything I could from IBM, one of the best in the business at marketing its solutions. Through a series of job changes, each one pulling me deeper into the marketing profession, I learned from mentors and experience (mistakes) about how marketing works in the real world. My analytical programming background never left me. As fascinated as I was with marketing, I was also troubled by how little was known about how to connect my marketing efforts to business results. I have seen marketing get credit for business results it didn’t necessarily produce, fail to get credit when it did produce results, and get blamed (fairly and unfairly) for its lack of contribution. In almost every case, judgments about marketing were based on opinion and conjecture, not data. This bothered me.

The inescapable conclusion was that the determination of marketing’s success was too arbitrary, too subjective. Even if marketing was performing brilliantly, without data to prove it, the CFO could still cut marketing’s budget. The CEO would question marketing’s contribution. The rest of the organization could easily view marketing as a luxury, not a necessity. When reviewing other corporate functions, such as development, human resources, or sales, I noticed they each had a specific set of metrics that created clarity about how well they were getting their jobs done. This was not the case in marketing. If marketing were to get taken to court and tried for its performance, what evidence could the defense provide in favor of marketing? Very little, it seemed.

What marketing needed was a set of metrics that moved the determination of its success out of the realm of subjective conjecture and into the world of objective fact. As a leader, I knew we needed more than the ability to say that we had churned out X number of assets or managed participation in Y number of trade shows and other, similarly vague attempts to connect our work to something the C-suite cared about. These attempts at accountability left questions in everyone’s minds about how well marketing was truly performing. The solution was analytics with a set of metrics that provided meaningful data about marketing’s contribution, so that even for those who might disagree with marketing’s methods, there was no argument about the results.

This book was born out of my past frustration and experience with how marketing manages its performance and how that performance is judged. I’ve written this book for the marketing organizations that have not yet started their analytics journey but know that they need to and are trying to figure out where to begin. I intend for this book to inspire marketers and marketing organizations to get moving and point them in the direction they should go. The advice contained here will provide them with a roadmap, as the title suggests, with recommendations on methods, metrics, and tools to consider on that journey. What this book will not do is provide detailed, turn-by-turn directions from start to finish of the marketing analytics journey. The reason is simple: each organization’s journey must be different. The things that define the speed, vehicle, and even detours of that journey vary from company to company.

This book is not for those who have already achieved maturity with their marketing analytics process, unless perhaps they want a nostalgic tour of where they’ve been. That still leaves a broad audience for the direction this book provides. A study I completed in partnership with VisionEdge Marketing just as this book went to publication reveals that just one in five marketing organizations have an effective analytics process in place. For everyone else—the 80 percent—this book will provide some encouragement and advice for starting and completing the marketing analytics journey. As the Chinese proverb attributed to Laozi states: “A journey of a thousand miles begins with a single step.” I hope this book helps many marketers take that first step.

Jerry Rackley

May 2015

Acknowledgments

I’m grateful to my Demand Metric colleagues, particularly John Follett and Jesse Hopps for their encouragement and support in the writing of this book. Likewise, to Dr. Joshua Wiener and Dr. Tom Brown in the marketing department at the Oklahoma State University Spears School of Business, who have encouraged, supported and mentored me.

There is a legion of marketing professionals who champion the cause of marketing analytics and the technology behind it: Scott Brinker, Laura Patterson, Clare Price, and Stephan Sorger, just to name a few. Their passion and enthusiasm has inspired me to put my thoughts and ideas into book form.

I owe a debt to those who have allowed me to be their marketer, both clients and employers. The experience gained from working alongside them in the marketing trenches has provided the grist for this book.

Finally, thanks to Jeff Olson, who found me, reached out to me, and convinced me to begin this book-writing adventure. Thanks to Rita, Susan, Laura, and the great team at Apress for their gracious and patient support throughout the process.

Apress Business: The Unbiased Source of Business Information

Apress business books provide essential information and practical advice, each written for practitioners by recognized experts. Busy managers and professionals in all areas of the business world—and at all levels of technical sophistication—look to our books for the actionable ideas and tools they need to solve problems, update and enhance their professional skills, make their work lives easier, and capitalize on opportunity.

Whatever the topic on the business spectrum—entrepreneurship, finance, sales, marketing, management, regulation, information technology, among others—Apress has been praised for providing the objective information and unbiased advice you need to excel in your daily work life. Our authors have no axes to grind; they understand they have one job only—to deliver up-to-date, accurate information simply, concisely, and with deep insight that addresses the real needs of our readers.

It is increasingly hard to find information—whether in the news media, on the Internet, and now all too often in books—that is even-handed and has your best interests at heart. We therefore hope that you enjoy this book, which has been carefully crafted to meet our standards of quality and unbiased coverage.

We are always interested in your feedback or ideas for new titles. Perhaps you’d even like to write a book yourself. Whatever the case, reach out to us at `editorial@apress.com` and an editor will respond swiftly. Incidentally, at the back of this book, you will find a list of useful related titles. Please visit us at [`www.apress.com`](http://www.apress.com/) to sign up for newsletters and discounts on future purchases.

The Apress Business Team

Contents About the Authorix Acknowledgmentsxi Introductionxiii [Chapter 1:​ Marketing Analytics](../Text/A978-1-4842-0259-3_1_Chapter.html) 1 [Chapter 2:​ The Marketing Analytics Landscape](../Text/A978-1-4842-0259-3_2_Chapter.html) 15 [Chapter 3:​ The Impact of Marketing Analytics](../Text/A978-1-4842-0259-3_3_Chapter.html) 31 [Chapter 4:​ The Marketing Analytics Process](../Text/A978-1-4842-0259-3_4_Chapter.html) 45 [Chapter 5:​ Getting Started with Analytics](../Text/A978-1-4842-0259-3_5_Chapter.html) 57 [Chapter 6:​ Return on Investment](../Text/A978-1-4842-0259-3_6_Chapter.html) 71 [Chapter 7:​ Marketing Dashboards](../Text/A978-1-4842-0259-3_7_Chapter.html) 87 [Chapter 8:​ Tools and Technologies](../Text/A978-1-4842-0259-3_8_Chapter.html) 103 [Chapter 9:​ Becoming Data Driven](../Text/A978-1-4842-0259-3_9_Chapter.html) 119 [Chapter 10:​ The Marketing Analytics Frontier](../Text/A978-1-4842-0259-3_10_Chapter.html) 133 Index147 About the Author

# 1. Marketing Analytics

What They Are and Why You Need Them

Imagine that you’re driving your car down the highway. You know where you’re going, and you have a general idea of which route to take, but you have no dashboard. You’re completely ignorant of how fast you’re going, how much fuel is in the tank, or even if the engine is overheating. If your “check engine” light is on to indicate some sort of problem, you don’t know about it. If luck is on your side, you can drive your car under these conditions to the end of your journey. But continuing to operate your vehicle over time without the information the dashboard provides is tempting fate. Eventually, a problem will occur that you can’t detect. Without the dashboard, you’re blind to the performance of the vehicle. Ultimately, driving this way will lead to consequences, some of them quite severe. You could easily end up in the ditch on the side of the road.

This vehicle metaphor applies to organizations and the analytics—their dashboards—that help them keep their marketing engines running effectively. A properly implemented analytics process helps a marketing organization avoid negative consequences. But analytics aren’t just about keeping marketing out of the ditch; analytics help optimize marketing’s performance. An analytics process, therefore, isn’t just about keeping the marketing organization out of trouble but helping it perform at its highest level.

Note

The marketing analytics process isn’t just about keeping the marketing organization out of trouble but helping it perform at its highest level.

For most companies, analytics are at the heart of how the business is managed. The finance department is perhaps most often associated with measurements and analytics with which the entire organization is familiar: revenue, profit, return on investment (ROI), return on equity (ROE), and many others. Manufacturing tracks metrics like output and defects. Human resources will measure employee retention and performance. Every department or division in a company uses analytics to make sure there is alignment with and progress toward goals. The outlier has often been marketing. Even today, 14 percent of marketing organizations don’t have an analytics process,[¹](#A978-1-4842-0259-3_1_Chapter.html#Fn1) a reflection of marketing’s historical reluctance toward analytics.

This book explores marketing’s attitude toward analytics, the challenges of implementing an analytics process, insights for embracing analytics in a meaningful way, and tools for getting the answers you need on the performance of your marketing program.

## Marketing Analytics Defined

The generic understanding of analytics is that they are measurements or numbers that indicate how a process is performing. This definition certainly holds true for marketing analytics, yet a deeper understanding of analytics helps marketing organizations take full advantage of what the process can do to drive better performance. To that end, here is my definition:

Marketing analytics is the process of identifying metrics that are valid indicators of marketing’s performance in pursuit of its objectives, tracking those metrics over time, and using the results to improve how marketing does it work.

The core components of this definition are worth examining more closely:

*   Valid indicators: There are many things about marketing’s work and results that are measurable. Not all of them, however, are true indicators of performance. The analytics process must determine which metrics have meaning and best represent the value that marketing creates for the organization.
*   Pursuit of objectives: The analytics process is ideally built to measure progress toward a set of objectives, a notion more fully explored in [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html). The objectives come first, followed by an identification of the relevant performance metrics.
*   Tracking metrics over time: The analytics process isn’t about taking a random, one-time snapshot of a performance measurement, but tracking measurements over time to monitor trends and direction of performance.
*   Improve how marketing works: There are several reasons a marketing organization might implement an analytics process, such as accountability or justification of resources, but the noblest and ultimately most valuable reason is to improve its performance.

This book refers to analytics and metrics, often interchangeably. These terms have similar meanings. Analytics are both the process and the collective output of that process—performance information with the ideal use as a management tool. Metrics are the “atomic unit” of analytics. The marketing analytics process consists of creating a series of metrics or measurements in specific areas.

As the definition for marketing analytics implies, it isn’t just a set of numbers. Instead, it is a process that has these primary components:

People: The marketing analytics process is created, executed, and managed by people who own it. In most marketing organizations, the process owner is the chief marketing officer (CMO) or the marketing director. Steps: The marketing analytics process consists of a sequence of steps. The steps that make up the marketing analytics process are described in [Chapter 4](../Text/A978-1-4842-0259-3_4_Chapter.html). Tools and technology: While the marketing analytics process isn’t necessarily complex, tools and technology help marketing organizations deliver greater value faster than they ordinarily might. [Chapter 8](../Text/A978-1-4842-0259-3_8_Chapter.html) discusses the tools and technology in greater detail. Input and output: Data feeds the process, with insights and decisions as the output of the process.

Like many processes, marketing analytics should have a definite beginning but no ending—it is a process that, once initiated, should continue indefinitely.

This discussion of marketing analytics has so far been conceptual. To provider greater clarity into how a marketing analytics process affects an organization, we will consider three scenarios of marketing organizations: one without an analytics process, one with a “pseudo” analytics process, and one with a real analytics process that is effective. As we examine these three organizations, we’ll look at the differences in how each is led, gets funding, operates, and makes decisions.

## No Analytics Process: Flying Blind

The first type of marketing organization we examine is the one that has no analytics process. In their metaphorical marketing vehicle, they have no dashboard at all. The CMO plots the organization’s course and pilots its way by instinct. Determining the success of marketing is completely subjective, and often quite political. For this reason, CMOs who choose to fly blind with respect to analytics are often adept at playing organizational politics. Survival depends on their ability to stay in the good graces of the CEO.

It’s easy to assume that such an organization would have no marketing success, but that isn’t the case. Despite the lack of an analytics process, these marketing organizations can have some impressive (albeit intermittent) successes. The problem is one of consistency. When these organizations succeed, it’s difficult for them to understand why, because marketing is essentially an experiment with no control variables. “We were lucky” is often the marketing department’s internal response to success. Conversely, when marketing fails to produce results, it’s equally difficult to explain the failure.

A common leadership scenario for these marketing organizations is for a new CMO to succeed one who was fired for failing to produce results. A problem, of course, is that since no analytics are in place, conclusions about results—or the lack thereof—is completely subjective. The CEO can simply conclude that the CMO is not performing. This seems terribly unfair to such CMOs, but if they have not implemented a set of marketing performance metrics, they have by default placed themselves on this slippery subjective slope.

The new, replacement CMO in these organizations typically has a tenure of about 18 months. The first six months are the honeymoon period, a time for the CMO to assess what’s been done and determine what needs to happen. The new CMO understands that business as usual will not create success, so it’s a time of change. During this period, the CMO will have the greatest creative latitude, because there’s a general understanding throughout the organization that the old methods no longer work. The leadership style of the CMO in this situation is typically an amalgam of intuition, creativity, and past experience.

The second six months in the new CMO’s tenure are spent implementing the new marketing agenda. The marketing team goes into execution mode with great expectations of success. This marketing organization does not use metrics or analytics to guide decision making or measure success, so how can it know if it achieves its goals? That determination remains subjective, but everyone in the organization will develop some intuitive feeling or use their own subjective measures to determine if marketing is succeeding.

The third six-month period in the tenure of a new CMO is the results phase. It’s the start of the second year of service, and by this time, the honeymoon is generally over. The general consensus is that results are due. Quite often there are favorable business results, but are they attributable to the work of marketing? This is a rhetorical question, because measurements don’t exist to support any credit marketing might take in producing favorable outcomes. When business is good, marketing usually gets a free pass on having to prove its contribution. The CMO can reasonably expect to remain employed for another 6–12 months.

When business is bad, however, the lack of evidence for marketing’s results is usually career-ending for the CMO. It’s entirely possible that the CMO’s marketing agenda was highly effective and prevented the company from even worse performance. But there is no solid ground on which marketing can stand to make that argument, and marketing has historically been a convenient scapegoat. Whether marketing is truly to blame is a moot point. The fate of the CMO and the future of the marketing team comes down to the subjectivity of the CEO, how persuasive the CMO is, and how many political allies he or she has. If the ax falls, a new CMO will come in and the cycle begins again.

Marketing organizations that operate in an analytics vacuum usually ride a funding rollercoaster. A new CMO gets the benefit of the doubt and finds a greater willingness to make a larger investment in marketing. But for most of these marketing organizations, the funding process is a grind. The annual budget battle for marketing funding is psychological warfare, since performance data doesn’t exist to create a business case for what marketing wants to do. Success securing the funding depends on the charisma, persuasiveness, and political alliances of the CMO. Usually, marketing has to settle for incremental increases in funding and resources, and then only in good years. If marketing wants to do something ambitious outside the status quo, the funding approval process can be a political gauntlet.

From an operational perspective, marketing organizations that eschew analytics tend to function without the benefit of a strategic plan. It’s not that they are directionless, but formal marketing planning yields plans that have measurements attached to their objectives. In this scenario, there’s a plan, but it is based on intuition and is very fluid. It also exists primarily in the head of the CMO, which means the rest of the company doesn’t have great insights into what marketing is up to.

This lack of clarity about the marketing plan has a long-term damaging effect on the marketing team. The absence of a codified marketing plan that is understood throughout the company makes it difficult for the marketing team to say “no” to the many internal, ad hoc requests that all marketing organizations get. Saying “no” to a marketing request—even one that the marketing team knows is silly—costs political capital. When no analytics process exists to prove its value, marketing needs all the friends it can get in the organization. This creates a reluctance for marketing to say “no” even when the team knows it should. When the rest of the organization becomes conditioned to getting what it wants from marketing, the team gets very busy—too busy, in fact. It is perceived as the part of the company with “its hair on fire.”

Note

A marketing organization that relies on political capital to execute its program rather than analytics ends up saying “yes” to requests that have dubious merit. Then, when such efforts fail, marketing gets the blame, hastening the day that marketing executives are shown the door.

This operational pattern demonstrates the value of having an analytics-based marketing plan. When such a plan is in place, it’s easy to refer to it as the reason for denying resources to these ad hoc requests that come from all corners of the company. Marketing can comfortably say “no,” because there’s a bigger, more important commitment: the marketing plan, which represents a success blueprint. Any requests that are inconsistent with the plan represent harmful distractions, to which the CMO and marketing team can say “no” with authority. But when no plan exists, as is often the case for these kinds of organizations, saying “no” comes across as arbitrary to those who request the team’s services and resources.

These marketing organizations are firmly on the intuition side of the decision-making fence. This means that virtually every decision has a significant degree of uncertainty associated with it. In fairness, even organizations that have an effective analytics process can’t remove all uncertainty from their decisions. Even though having no analytics process means that decisions are essentially educated guesses, these organizations have a lot of conviction about the guesses they make.

## A Pseudo-Analytical Marketing Program: Half the Gauges Are Dark

The next type of marketing organization we examine is one that has a pseudo-analytics process in place. In the metaphorical vehicle that is conveying this marketing team, there is a dashboard, but some of the gauges aren’t working or are simply ignored. The other possibility is that the marketing team doesn’t know how to read the gauges and understand what they mean. In many ways, this marketing team resembles the story of the Emperor’s New Clothes. Because they have some numbers related to marketing, they feel clothed, but many in the company recognize their nakedness.

As with the marketing organizations that are flying blind, these pseudo-analytical organizations can produce success. The difference is that these organizations attempt to use metrics as part of its marketing process. Some numbers related to what marketing is doing are leveraged, often with a high degree of devotion. The flaw in their approach, however, is that the numbers aren’t true indicators to marketing’s performance. There is no clear connection between the metrics, which reflect some marketing activity, and business results. The simple fact is that some metrics are in use that can actually create a false sense of accomplishment. The marketing team understands that it should have some metrics that define its performance. So it feels good about having some, but the pseudo-analytics process it uses doesn’t produce metrics that have real meaning or offer strategic insights to the team.

There are two common leadership scenarios in marketing organizations using pseudo-analytics. A leader who knows analytics are important but just doesn’t understand how to define meaningful performance metrics represents the first scenario. A lack of experience, skills, time, or mentoring prevents this CMO from understanding how to use analytics as a performance gauge and gain insights for improving marketing’s effectiveness. What tends to happen is that this leader latches onto whatever analytics are easily available. A range of interesting analytics are often forged into some kind of dashboard, often including things like:

*   Productivity measures: projects completed, ads designed, meetings held, and so on.
*   Marketing measures: web analytics (e.g., page views, unique visits), social media follows, direct mailers sent, and so on.
*   Internal client satisfaction: some measure of how well the marketing team is serving the organization, often a measure of responsiveness.

These metrics are very activities-centric, but they don’t provide much insight into how marketing is actually contributing to achieving the organization’s revenue goals. The executives who are recipients of this organization’s analytics dashboard may nod and smile while reviewing them, but they have unexpressed frustration about what the metrics truly mean. In the boardroom where conversations are about revenue, earnings, ROI, EBITDA,[²](#A978-1-4842-0259-3_1_Chapter.html#Fn2) and other key financial metrics, no one, yet alone this marketing leader, knows how to connect the marketing team's activities to business results.

The other leadership scenario seems more nefarious, involving a marketing leader who uses analytics as a smokescreen. This CMO understands that the organization expects marketing to use analytics, so he or she does, throwing up a smokescreen of metrics that obscure what marketing is really doing. As with the first leadership scenario, the analytics process in use by this CMO also employs metrics that don’t really connect marketing activities to business results. If there is a difference in this leadership scenario and the first one, its that sometimes analytics are reverse-engineered to help justify the CMO’s predetermined course of action. They simply provide cover for the CMO.

This second leadership scenario does seem like the product of a scheming CMO, but it’s rarely the result of some evil plot. Instead, it’s a product of fear—a fear of having analytics that reveal marketing’s failure to perform. This fear is fueled by an insecurity—that he or she can’t risk having metrics show that marketing doesn’t work or doesn’t know what it’s doing. This fear is legitimate, and anyone who has been part of the marketing community long enough can probably recount a story or two about how analytics were used punitively against the marketing organization. In these situations, analytics aren’t the problem; the culture is to blame.

With either leadership scenario, the pseudo-analytics process creates opaqueness, and the CMO is content with that outcome. Since no one outside of marketing can really understand what the analytics coming out of marketing mean, they have to go to the CMO for interpretation. The explanation these inquiries produce sound something like, “It’s hard for someone without a marketing background to understand them, but trust us: these are good numbers.” As long as business results are reasonably good, most people in the organization accept this explanation, even while scratching their heads. The truth, however, is that marketing organizations that use pseudo-analytics are on the same, slippery, subjective slope as those who use no analytics. When financial pressures cause executives to inspect with a critical eye what’s really happening, marketing can’t answer the difficult questions about its contribution.

Note

Marketers using pseudo-analytics are on a slippery, downward slope along with those using no analytics. When numbers-oriented executives cast a critical eye on marketing efforts, marketing managers are hard-pressed to come up with answers that explain their value or show how they contribute to financial results.

The funding situation with pseudo-analytics doesn’t differ much than that for organizations with no analytics process. It’s still an annual battle for incremental budget increases. One difference is that marketing can cite improvements in the metrics as justification for funding requests. Never mind that the analytics put forth by marketing may not really indicate how marketing is performing; the fact that there are numbers at all lends a veneer of credibility that marketing wouldn’t otherwise have. Even though the executives who approve the budget don’t understand the work of marketing well, they will often accept their budget rationale on faith.

For big funding requests, these marketing organizations fare better than those using no analytics. Because ambitious projects require some sort of business case, justification is based on a set of metrics that tangentially support what marketing wants to do. Numbers, even the wrong ones, are persuasive when no one understands how to challenge them. Any discussion of ROI, however, is just speculation, because there are no metrics—or a culture of using them—that allows for the calculation or even estimation of ROI.

Operationally, these marketing organizations are busy. Busy-ness is the common denominator of any marketing organization, but these organizations aren’t necessarily busy doing the right things. Without the guardrails of a good analytics process, the organization is never sure of whether it is on the right path or how effective it really is. The pseudo-analytics it uses are oriented more toward tracking activity instead of results. Units of marketing tasks performed, such as mailers sent, web pages updated, brochures printed, or ads placed indicate how busy marketing has been, but not what it produces in the way of results.

As for decision making, these marketing organizations rely both on intuition and the pseudo-analytics, but more on the former than the latter. They are reluctant to lean too heavily on their analytics, because they don’t have complete confidence in them. Of greater concern is having the analytics become master over the marketing department, so marketing maintains an arm's-length relationship with its analytics. As long as they provide cover for what marketing wants to do, the analytics are useful. But allowing analytics to influence decision and strategy is unthinkable, because it risks putting analytics in the driver’s seat instead of the CMO.

This orientation puts marketing squarely on the fence with respect to decision making. It doesn’t have the benefit of a good analytics process, nor does it rely fully on intuition but instead a murky combination of pseudo-analytics and gut feeling. This produces uncertainty, and the bigger the decision marketing has to make, the more uncertainty there is about it. For this reason, these organizations tend to have less conviction about the big decisions they make than those with no analytics process.

## A True Analytics Program: The Real Deal

> If I could give marketers everywhere one tip on evaluating and using marketing analytics, it would be to make yourself data-informed, but not exclusively data-driven because the power of serendipity and of investing in unmeasurable marketing can be incredible. —Rand Fishkin, Moz

The final scenario examines a marketing organization that has a real analytics process in place and uses it. What is meant by “real” is a process that tracks metrics that provide true and direct indicators of marketing’s performance and contribution. Using these metrics, the marketing team and the company it serves can determine with precision how marketing influences revenue. Furthermore, these analytics are a management tool that helps marketing optimize its efforts and produce the best possible outcomes. In this marketing team’s metaphorical vehicle, all the dashboard gauges and indicators work, and the team knows how to read them. When the check engine light comes on, immediate problem determination and resolution takes place. The metrics and performance data this process produces not only guides the marketing team, it is shared freely with the rest of the organization.

Unlike marketing organizations that are flying blind or using pseudo-analytics, these organizations consistently produce success, and because they use an effective analytics process, they have no difficulty demonstrating their value to the rest of the company. Success is almost never the result of luck or a good guess by the CMO. Instead, it comes from using analytics to measure what marketing does, and then pivoting when the analytics indicate a need for it, or staying the course when managers confirm it is the right one.

It’s easy to assume that these organizations put analytics in the driver’s seat and simply go wherever the numbers lead them. Analytics are critical success factors for marketing, but they don’t call the shots here. The correct understanding of this relationship is to see the CMO as the true master of marketing, with analytics as a trusty servant. As my colleague Stephan Sorger put it in a webinar we taught, “Analytics are good servants, but poor masters.”[³](#A978-1-4842-0259-3_1_Chapter.html#Fn3)

Strategies are devised and implemented, campaigns developed and launched, all with a reliable set of built-in metrics to measure impact.

The output of the analytics process is constantly monitored to determine:

Performance: what do the metrics reveal about the results marketing is producing? Validity: are the right metrics in use to provide a true and precise indication of performance?

If the metrics reveal that a marketing initiative or campaign isn’t working, changes are made to produce better results. Marketing doesn’t just continue to do the same thing hoping that the results will magically improve. Instead, it recognizes what the analytics process is telling it: change must occur. Likewise, the metrics themselves are constantly under scrutiny to make sure they are accurate indicators of performance. These marketing organizations know that in the area of analytics, it’s easy for expediency to take over, leading to measuring what’s easy instead of what’s relevant.

Note

Analytics are useful and even essential to a marketing program. That doesn’t mean the numbers tell you each and every move you need to make. The CMO can’t just play “follow the leader” with analytics.

The use of analytics is part of the marketing culture that everyone embraces. Culture is a function of leadership, so the CMO sets the expectations and models the use of analytics as part of the marketing culture. The CMO is the analytics process owner, and ideally a thoughtful person who understands not to use analytics as a whip to punish poor results. When analytics are used punitively, they’re immediately rejected, or manipulated to only generate favorable data, eroding the value of the process. To keep the process working like it should, these CMOs eliminate the fear of negative consequences from using analytics to expose the poor performance of a marketing initiative. Instead, the CMO creates a culture around analytics that rewards identifying problems and underperformance so resolutions can occur.

The budgeting process is rarely just a formality for any type of marketing organization. However, those with analytics at the core of their culture have a much easier time getting funding. One reason is because the value of marketing is known: the analytics its uses allow it to clearly show the entire organization how it helps create revenue. Because it is able to tie revenue directly to its activities, marketing’s ROI is known. Although it may take some effort to analyze and present the data, because these marketing organizations have a real analytics process, justifying spending is never speculation. It’s much easier to do because the necessary data is available.

The analytics discipline of these marketing organizations are also helpful when it comes to justifying ambitious, out-of-budget projects. Sometimes, the organization will sense an opportunity that requires funding not in the budget, as well as the agility to exploit it quickly. The C-suite is far more likely to make these investments when marketing has the ability to measure the results. This ability is no guarantee that the endeavor will succeed, but at least the organization will know the outcome with certainty. There’s no fuzzy speculation about what kind of returns are coming from these investments.

These organizations tend to operate with greater authority and conviction than those that are flying blind or using pseudo-analytics. For any significant marketing activity or campaign, a prerequisite is identifying metrics to determine its success. This operational approach builds in a healthy level of skepticism about success: the marketing team doesn’t usually celebrate success—or condemn something as a failure—unless the metrics provide the proof.

Using the analytic process as a guide, the marketing team maintains a steady focus on producing results, continuously refining what it does to constantly improve, generate more revenue, or increase the margins. This orientation feels very different from the first two types of organizations where activity is celebrated. When analytics are at the heart of the marketing team’s culture, activities are meaningless unless they produce a result the business cares about. One effect this attitude generates is an intolerance for empty activities or busy work that produces no value.

It’s easy to get a mental picture of these marketing organizations as dispassionate number crunchers that do whatever the numbers dictate. That picture is an inaccurate portrayal of how they make decisions. Of course the numbers matter, but an organization that just followed the numbers wouldn’t provide the leadership or creativity that an organization needs from its marketing function. What happens instead is that the CMO drives the development of strategy, usually in close collaboration with the marketing team and key organizational stakeholders.

Part of this decision making is always ample consideration given to determining the right metrics for evaluating how the strategies and tactics are working. Marketing analytics don’t remove all the risk from what marketing does, but they enable marketing to more quickly and accurately determine if results are within acceptable risk parameters. For this reason, these kinds of marketing organizations are usually more comfortable taking risks than the other two types.

Tip

A good analytics program will enable you to take more risks—comfortably—in bets that pay off.

There’s no mistaking on which side of the fence these marketing organizations are: firmly on the analytics side. This doesn’t mean that intuition plays no role in how these organizations operate, but it does mean that intuition is always paired with a set of metrics. Intuition has value and remains a key part of how these marketing organizations develop strategy. Intuition may lead, but verification metrics always follow. For this reason, these organization know better than the other two the degree to which they can trust their intuition.

Table [1-1](#A978-1-4842-0259-3_1_Chapter.html#Tab1) provides a summary of each type of marketing organization this chapter examines.

Table 1-1.

Three Marketing Analytics Scenarios: No Analytics, Pseudo-Analytics, and Real Analytics

| Type | Leadership | Operation | Funding | Decisions |
| --- | --- | --- | --- | --- |
| None | Intuitive | Busy-ness | Negotiated political process | Educated guesses |
| Pseudo | Intuitive; analytics smokescreen | Busy-ness | Negotiated political process | Educated guesses |
| Real | Intuitive; analytics proof | Results | Based on business case | Informed by analytics |

### Not Three of a Kind

The three kinds of marketing organizations put under the microscope in this chapter have one thing in common: they all fall within the “marketing” box on the organizational chart. While those that are flying blind and those that use pseudo-analytics share several characteristics, there are few similarities between them and the one that uses real analytics.

Marketing professionals that have had experience working in all three kinds of organizations almost always find the last kind the best place to work. The reason is because in the first two kinds, marketing is viewed as an expense, a necessary one perhaps, but still an expense. Should economic pressures affect companies that view marketing this way, the marketing functions is one of the first place the company makes cuts.

By contrast, in marketing organizations that use a real analytics process, the value of marketing is known and understood by the entire company. Here, marketing is a revenue center, and its performance makes it an invaluable contributor to the company’s success. Marketing organizations that do not learn how to transform themselves from cost centers to revenue centers are transient. Their future is never certain; their existence is often in jeopardy. The reason marketing organizations should adopt a real analytics process is so they can make the transformation from a cost to a revenue center, and provide ongoing proof of that transformation. The remainder of this book discusses how to create such a marketing organization.

Footnotes [1](#A978-1-4842-0259-3_1_Chapter.html#Fn1_source)

“Sales & Marketing Analytics: Gauging and Optimizing Performance,” p. 7, Demand Metric benchmark report, December 2013.

[2](#A978-1-4842-0259-3_1_Chapter.html#Fn2_source)

Earnings before interest, taxes, depreciation, and amortization.

[3](#A978-1-4842-0259-3_1_Chapter.html#Fn3_source)

See [http://​www.​demandmetric.​com/​content/​eworkshop-learning-love-analytics](http://www.demandmetric.com/content/eworkshop-learning-love-analytics).

# 2. The Marketing Analytics Landscape

Facts, Attitudes, and Challenges

When viewing marketing as an entity, you can perceive it as having a persona, similar to the three types [Chapter 1](../Text/A978-1-4842-0259-3_1_Chapter.html) describes: no analytics, pseudo-analytics, or real analytics. These personas represent a spectrum of how the marketing team operates and creates value. On one end of the spectrum, marketing is reactive and operationally oriented, managed by the perpetually busy, “hair on fire” type of manager. Occupying the other end of the spectrum are organizations where marketing is a strategic asset, providing leadership to the whole company through a visionary leader executing a plan that aligns with corporate strategy. The foundation of that plan is a set of analytics that functions as guardrails to help marketing stay on course.

On the operational side of this spectrum, marketing delivers incremental value at best, and it faces a constant struggle to justify not only its budget but its very existence. On this spectrum’s strategic side, marketing is a major driver of the company’s success. Because it uses analytics effectively, marketing has no trouble proving its worth. As a strategic, revenue-generating asset, marketing easily justifies its existence and provides a return on the investment made in it.

The relevance of any marketing organization depends on its ability to shift from an operational orientation to a strategic one, and many marketers aspire to help their organizations make this transition. Making this shift is imperative for marketing to change its perception as a tolerated expense to that of a valued revenue center. From a leadership vantage point, there’s a lot riding on marketing’s ability to make this shift, because operational marketing organizations don’t enjoy much influence.

Most marketers are not ignorant of the need to make this shift, but the pace of change at times seems absolutely glacial, frustrating the marketing team and the leaders to whom they are accountable. Generating lift for marketing are a growing array of technology solutions, tools, methods, channels, skills, and leadership that marketers are developing or exploiting. Figure [2-1](#A978-1-4842-0259-3_2_Chapter.html#Fig1) depicts the tremendous diversity of the marketing technology landscape, with virtually every solution having the capability to participate in or contribute data to a marketing analytics initiative.

> Technology decisions and marketing strategy are intertwined. —Scott Brinker, Rise of the Marketing Technologist

![A978-1-4842-0259-3_2_Fig1_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig1_HTML.jpg)

Figure 2-1.

The Chiefmartec.com marketing technology landscape reveals the diversity and complexity of the marketing solutions landscape. Used with permission

The very complexity of the marketing technology landscape, however, is daunting because marketers assess myriad solutions that can help them attribute business results to their activities. The technology challenges, however, are temporary, and a marketing analytics initiative can’t take flight without some supporting systems and sources of reliable data. What creates the most drag against a marketing analytics initiative are attitudes, usually within organizations but sometimes within the organizations those marketers serve.

Modern marketers generally understand how important it is to have the marketing team function as a revenue center, where that revenue contribution is quantified and reported through an analytics process. When marketing exists as an expense instead of a revenue center, it’s in jeopardy because it ranks high on the short list for cutbacks when the company experiences financial pressure. Functioning as a revenue center helps marketing not only survive but thrive when the company needs its services in difficult times. For this reason, even when CMOs haven’t completely figured out how to transform their team into a revenue center, they steadily press on with this shift because it is imperative to the long-term survival of marketing.

From a macro perspective, how is marketing viewed today—as an expense or a revenue center? Demand Metric Research Corporation completed a study[¹](#A978-1-4842-0259-3_2_Chapter.html#Fn1) during the first quarter of 2014 that provides the answer. The first thing this study attempted to learn is the financial perception of marketing inside a company. Figure [2-2](#A978-1-4842-0259-3_2_Chapter.html#Fig2) displays the results from more than 250 study participants, most of whom were marketers, indicating how their marketing function is perceived inside their organization.

![A978-1-4842-0259-3_2_Fig2_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig2_HTML.jpg)

Figure 2-2.

The dominant internal view of marketing is as an expense

This data is a good indicator of the current state of marketing analytics, because the best tool for changing the perception of marketing as an expense is an analytics process. Further analysis of the data in this study reveals that of those organizations viewing marketing as an expense, half don’t use analytics at all or only to a slight extent. For whatever reasons, they are denying themselves the best means to guide their efforts and show accountability to the rest of the organization.

By contrast, for the organizations represented in Figure [2-2](#A978-1-4842-0259-3_2_Chapter.html#Fig2) that perceive marketing as a revenue center, merely 16 percent report not having an analytics process. Any company that has properly implemented customer relationship management (CRM) and marketing automation systems, and also manages the quality of data in these systems, will have access to the information necessary to demonstrate not just relevance but revenue impact. There is no other way for the modern marketing organization to function.

Note

There is a strong relationship between marketing’s perception as a revenue center and the use of an analytics process.

Modern marketers won’t argue that the way for the marketing function to operate is as a revenue center, one that is indispensable to the rest of the organization. What characterizes an “indispensable” marketing organization? We need some characteristics more detailed than “produces revenue” to understand how these kinds of marketing organizations operate and differ from the ones perceived as cost centers. Figure [2-3](#A978-1-4842-0259-3_2_Chapter.html#Fig3) reveals the three key characteristics of marketing organizations described as “we would not achieve our business objectives without marketing’s contribution”—or in other words, indispensable—in a survey on marketing analytics.[²](#A978-1-4842-0259-3_2_Chapter.html#Fn2) When using these three characteristics to compare these indispensable marketing organizations to all marketing organizations, there are significant differences.

![A978-1-4842-0259-3_2_Fig3_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig3_HTML.jpg)

Figure 2-3.

“Indispensable” marketing organizations exhibit three characteristics: strategic orientation, sales and marketing alignment, and analytics-driven

The first characteristic of an indispensable marketing organization is strategic orientation. This implies that there is a plan that drives what marketing does. Ideally, this plan results from a strategic planning process involving the key stakeholders in the company. This plan aligns very closely with corporate goals and objectives, and there are milestones and measurements to track progress.

By contrast, marketing organizations that exist as cost centers are more likely to have an operational orientation. This doesn’t mean they have no plan, but the plan is often ignored or even abandoned. These marketing organizations are request-driven and more reactive. They resemble the type of marketing organization described in [Chapter 1](../Text/A978-1-4842-0259-3_1_Chapter.html) that is flying blind with respect to analytics.

The second characteristic of indispensable marketing organizations is that they have a high degree of alignment with the sales teams that they support. They work together, and not apart in silos, and they have compatible or even shared goals.

The third characteristic of indispensable marketing organizations is that they are very or completely analytics-driven. Analytics are at the heart of how these marketing organizations work. They rely on analytics and metrics to validate their efforts and show accountability to the rest of the organization.

The marketing organizations exhibiting these characteristics are not at risk of having the perception that they are an expense. Furthermore, analytics don’t occupy a silo separate from other characteristics. Analytics enable the first two characteristics discussed above. We’ll examine the current state of marketing analytics to see how prevalent these indispensable marketing organizations are.

## The State of Marketing Analytics

Marketers will always face the challenge of demonstrating value and relevance to the organizations they serve. Marketers, although sometimes reluctantly, generally agree that an analytics process provides the most compelling evidence of their contribution. What is the current state of analytics across the marketing community at large?

Figure [2-4](#A978-1-4842-0259-3_2_Chapter.html#Fig4) provides a graph, summarizing the views of more than 700 company presidents, CEOs, CMOs, vice presidents of marketing, marketing directors, marketing managers, sales managers, and product and brand managers on the status of marketing analytics in their companies. A survey[³](#A978-1-4842-0259-3_2_Chapter.html#Fn3) of this group inventoried the state of their analytics efforts using the following response categories:

![A978-1-4842-0259-3_2_Fig4_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig4_HTML.jpg)

Figure 2-4.

Most marketing organizations are trying to do something with analytics, with varying degrees of success

*   Avoidance: We’re trying to avoid analytics unless required to use them.
*   Searching: We want to do something and are searching for the best way to start.
*   Experimenting: We’re experimenting to identify the best set of analytics, but they aren’t yet influencing behavior or activity.
*   Low visibility: We’re tracking meaningful analytics, but they don’t yet have visibility outside of the marketing organization.
*   High visibility: We’re tracking meaningful analytics, and they have visibility throughout the organization.

In this era of modern marketing, just over a third of companies report that they are tracking meaningful analytics. The same group of participants were also asked to rate their marketing process using a scale in which 1 equals “avoidance” and 5 equals “meaningful and visible results.” This analysis reveals that large companies (more than 1,000 employees) report greater analytics progress with a mean score of 3.46, compared to small companies (100 employees or fewer) whose mean score was 3.17.

There is also a relationship between company size and the state of analytics:

*   33 percent of small companies are either avoiding analytics or searching for the best way to start.
*   26 percent of medium-sized companies (between 101 and 1,000 employees) fall into these same two categories.
*   19 percent of large companies fall into these categories.

It seems that the larger the company, the more likely that its marketing department is using analytics. Small companies may feel less compelled to pursue a marketing analytics initiative for a number of reasons. Quite often, there is an “all hands on deck” mentality in small companies, and everyone pitches in to do whatever is necessary for the company to succeed, and this includes marketing. The various functions within a small company usually have proximity to one another, literally working side by side. In this setting, everyone seems to know what everyone else is doing, so the need for the accountability that analytics provides isn’t as urgent.

As we consider the state of marketing analytics, the amount of funding analytics initiatives receive is a good indicator of importance. The things companies invest in reveal their priorities and the seriousness of their endeavors. Although marketing analytics don’t represent a massive outlay, it does require some funding and resources if the process is to have impact. The data summarized in Figure [2-5](#A978-1-4842-0259-3_2_Chapter.html#Fig5) provide a view of how much of the marketing budget is earmarked for analytics.[⁴](#A978-1-4842-0259-3_2_Chapter.html#Fn4)

![A978-1-4842-0259-3_2_Fig5_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig5_HTML.jpg)

Figure 2-5.

Almost one-fourth of organizations have no allocation for analytics in their marketing budgets

When analyzed further, the data in Figure [2-5](#A978-1-4842-0259-3_2_Chapter.html#Fig5) provides insights into the relationship between analytics funding and effectiveness. Eight percent of participants in this study are investing more than 10 percent of their marketing budgets on marketing analytics. For this elite group, 50 percent of them also report they are tracking meaningful analytics that have visibility throughout the organization (Figure [2-4](#A978-1-4842-0259-3_2_Chapter.html#Fig4)). This represents a level of analytics maturity that is almost double that for organizations spending between 6 and 10 percent of their budget and triple that of organizations spending less than 5 percent of their budget. This analysis makes clear that more investment leads to greater visibility of the analytics data. Does that visibility also equate to better results? Although visibility is important, results are what matters.

Additional analysis of the budget data in Figure [2-5](#A978-1-4842-0259-3_2_Chapter.html#Fig5) shows an apparent point of diminishing returns on investing in marketing analytics. The marketing organizations that are allocating between 6 and 10 percent of their budget on marketing analytics are getting the best results, with 51 percent of them reporting the highest rate of improving marketing results with analytics. For the elite group that is spending more than 10 percent of their budget, 45 percent report this highest rate of improvement, and for those budgeting less than 5 percent on analytics, 40 percent fell within this highest rate of improvement. It seems, therefore, that the sweet spot is to allocate between 6 and 10 percent of the marketing budget toward analytics.

## Attitude Toward Marketing Analytics

The presence of a marketing analytics process within a company isn’t necessarily an indicator of a favorable attitude toward analytics. Indeed, when it comes to marketing analytics data that is measured and tracked, almost one-fourth of companies report that “no one seems to be using it.”[⁵](#A978-1-4842-0259-3_2_Chapter.html#Fn5) In some companies, executives send the marketing team on a forced march to analytics. In such a case, resistance to the analytics process is quite high. Although the process often gains some converts, it’s still an uphill climb against the passive-aggressive response marketing often exhibits to having analytics forced upon it.

Less common is the pro-analytics marketing team that can’t get the attention of the rest of the company about the value the process can provide. Here, too, it is likely that over time, the marketing team can win over converts to the process. In either case, real results from the analytics process are very convincing.

These two positions reflect the varying attitudes that can exist within an organization about analytics. For any initiative, the biggest barriers are often attitudinal, so as we review the marketing analytics landscape, it’s helpful to understand the prevalence of the attitudes that shape it. The Demand Metric study also gives insight into the range of attitudes that exist toward analytics and how widely they are held. Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6) summarizes this data about attitudes.

![A978-1-4842-0259-3_2_Fig6_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig6_HTML.jpg)

Figure 2-6.

By a wide margin, the prevailing attitude toward marketing analytics is that they are vitally important as a management tool

Of the five attitudes presented in Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6), only two represent negative attitudes, and those are held by just 8 percent of the survey sample. The remaining three attitudes are positive. In the following paragraphs, we’ll explore these attitudes in detail.

The least prevalent attitude contends that it is impossible to define a meaningful set of marketing analytics. To assert with absolute certainty that it isn’t possible to derive a set of metrics that have meaning is simply a sophisticated declaration of unwillingness. Of course it is possible for any marketing organization, through adequate thought and planning, to identify a set of metrics that are effective performance indicators. Although this attitude is rare—perhaps because few CEOs will tolerate it—it is associated with CMOs who don’t want facts or analytics data to interfere with their marketing agenda. To simply dismiss analytics with a declaration that it isn’t possible to measure the results of marketing’s efforts is either complete ignorance or arrogance. Although it’s true that barriers may exist to accurately measuring marketing’s business impact, none of them are insurmountable.

The second attitude from Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6) that is negative also isn’t very prevalent: just 5 percent of the sample in this study report that analytics are too easily misinterpreted by those outside of marketing. There are three drivers that often account for this attitude. The first is a legitimate fear that those who don’t understand or appreciate the analytics might use them inappropriately. The second is the fear that the analytics will reveal marketing’s poor performance. The third driver is arrogance.

The first underlying reason of the attitude that analytics are too easily misinterpreted by nonmarketers is a healthy fear of the consequences. Analytics, particularly when first implemented, don’t always have a happy story to tell. If marketing is completely transparent with its analytics data, some who are outside the marketing group could seize the opportunity to use the data for political gain. What really needs to occur, however, is for marketing to have the chance to use the data to improve, and this may require completing several cycles of measurement and analysis. It’s harder for this to happen if a dysfunctional culture exists in the company. In such a culture, marketing may have good reason to fear complete analytics transparency. It is an unfortunate fact that in corporate environments, not all cultures are healthy. Anyone who has been in marketing long enough can probably share experiences of communicating analytics and performance data transparently, and then getting punished for it.

A second driver of the attitude that analytics are too easily misinterpreted by nonmarketers is insecurity. The roots of this insecurity come from the CMO knowing that meaningful analytics will reveal marketing’s poor performance. Even in the absence of analytics data, marketers have pretty good intuition about the kind of impact their work is having. A survival strategy for marketing managers who are insecure about their team’s performance is to avoid analytics and front the team with a flurry of activity and communicate in platitudes about the results. It strikes fear in the hearts of these CMOs when the CEO suggests—or decrees—the use of analytics. An almost automatic response in such situations is “analytics are too easily misinterpreted by nonmarketers.” The goal is to portray marketing as some sort of dark art, a practice so arcane that only those within its fraternity can hope to understand the analytics. The irony here is that a proper analytics initiative would provide this marketing organization with improvement data, vanquishing the fear of being “outed.”

A third driver is simply arrogance. When arrogance is in play, marketing is essentially communicating to the rest of the organization that what it does is beyond comprehension by outsiders. From the outsider’s perspective, there isn’t a perceptible difference between insecurity and arrogance as the driver of this attitude, because both portray marketing as beyond comprehension. In either case, however, the net effect is to build a smokescreen to hide marketing’s true performance, and this comes from a willful decision to oppose marketing analytics. The fact remains that proper education and communication about the meaning of the marketing analytics data removes the concern over misinterpretation.

The third attitude depicted in Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6) acknowledges that analytics could provide marketing with some needed credibility. Marketing organizations that don’t use analytics data to account for their contribution are susceptible to having their credibility questioned. At worst, marketing’s very existence is threatened when its credibility within the organization it serves is questioned. At best, when marketing has a credibility problem, it will struggle to gain acceptance for the strategies and campaigns it wishes to deploy. The 20 percent of the survey sample who chose this attitude response option acknowledge one of the benefits of having an analytics process: the credibility of having data to prove marketing is effective. With that credibility comes a great deal of latitude and empowerment for the CMO. [Chapter 3](../Text/A978-1-4842-0259-3_3_Chapter.html) explores this issue in greater detail.

The fourth attitude depicted in Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6), held by 25 percent in the survey’s sample, is that analytics could help show the value marketing creates. This attitude communicates the reality about how marketing organizations can demonstrate their effect on revenue—through analytics. There is no other way for a CMO to account for the share of revenue directly attributable to marketing except through a set of metrics that link marketing’s activity to business results. Every marketing organization should aspire to show proof of its value to the company. Even when marketing organizations create intrinsic value through inspiring, creative campaign development and execution, they still need to offer proof of the value they deliver. An analytics process provides that proof.

The fifth and final attitude depicted in Figure [2-6](#A978-1-4842-0259-3_2_Chapter.html#Fig6) is the most widely held. Almost half the survey sample responded that analytics are vitally important as a dashboard to help manage marketing processes. This attitude represents the ultimate goal of an analytics process: using the output from it as a management tool to continuously improve what marketing is doing. Analytics help show that marketing has credibility, and they are a way to demonstrate marketing’s value. These benefits flow out of using analytics as a dashboard to guide marketing’s efforts. This attitude, therefore, represents the primary analytics goal for marketers. Build an analytics process that serves as an effective dashboard to guide marketing’s work, and credibility and demonstrable value will follow.

## Marketing Analytics Challenges

It’s easy enough to find some marketing metric on which to measure and report. However, creating an analytics process that measures and tracks meaningful things is complicated and sometimes difficult for most companies. The challenges to building an effective analytics process are broad in scope, ranging from attitudes, skills, technology, and resources. Just 6 percent of companies claim not to have any challenges related to marketing analytics.[⁶](#A978-1-4842-0259-3_2_Chapter.html#Fn6) Figure [2-7](#A978-1-4842-0259-3_2_Chapter.html#Fig7) summarizes the biggest challenges that companies have with their marketing analytics efforts.

![A978-1-4842-0259-3_2_Fig7_HTML.jpg](Images/A978-1-4842-0259-3_2_Fig7_HTML.jpg)

Figure 2-7.

Ninety-four percent of organizations report facing some kind of challenge with marketing analytics

It’s worth noting what isn’t a big challenge to marketing analytics: buy-in or support. Over half the participants in the survey from which this data is drawn are marketing leaders: CMOs, brand managers, vice presidents of marketing, marketing directors, or marketing managers. What they tell us through this survey is that getting the buy-in from their own teams is negligible as a challenge. Members of the marketing team understand that it is in their best interest to have an analytics process. The challenge of securing the support of the executive or management team is also negligible. Rightly so, because what executive or manager would essentially communicate to the marketing team that it doesn’t desire accountability, ROI, or measurable value by failing to encourage an analytics initiative? The lack of support from above or below is not a serious challenge to analytics for CMOs.

The greatest challenge is not having the systems or infrastructure to measure and track marketing analytics. The modern marketing organization uses multiple channels, most of them digital, and the campaigns and content that marketing produces are consumed across multiple devices. The problem this environment creates is not a lack of analytics data, but a plethora of it. The volume and variety of data coming at the marketing team can leave it feeling like it’s drinking from the fire hose. To capture and fully exploit it as part of an analytics process requires some technology. The data is simply too vast and varied to deal with through manual processes. The analytics initiative needs a technology assist to capture the data and make sense of it. [Chapter 8](../Text/A978-1-4842-0259-3_8_Chapter.html) explores some of the tools, systems, and technologies that the modern marketer can use to overcome the systems challenge of marketing analytics.

The lack of time or staff for marketing analytics represents another significant challenge. Implementing an analytics process is a strategic endeavor, and for busy organizations, taking the time to plan and execute it seems analogous to changing the tires on the vehicle while it’s still in motion. These busy marketing organizations look forward to a time when things are calm enough to allow them time to conduct the proper planning to implement analytics well. However, there is no such thing as slack time in busy organizations. Most are busy because in the absence of an analytics process, activity is the evidence of progress. The organizations that claim to not have the time for analytics are the very ones that need it most. To them, the notion of stopping the marketing vehicle, even to make enhancements that will enable better performance, is anathema.

Staffing for analytics is a challenge that reflects priorities. Few marketing organizations—even those that guide their efforts with an effective analytics process—claim to have all the staff they need. An analytics process is the foundation for marketing excellence and achievement. It’s unthinkable to fail to devote staff to the process because this factor is critical to marketing’s long-term success. Staffing the analytics function should have top priority, because the output of the process can help make the business case to staff and fund other areas of marketing.

The lack of knowledge or expertise presents another challenge to marketing analytics. The question marketers should ask is: knowledge or expertise of what? As long as there has been a marketing discipline, it’s been important to understand the customer and products well. This will never change. When analytics enter the picture, it’s natural to assume that statistical prowess is necessary. It certainly isn’t a liability to understand statistical processes, but neither is it an imperative.

What’s increasingly critical for the modern marketer are technical skills, or perhaps more accurately, technology skills. The ability to effectively use the software and technology at our disposal is fast becoming the critical distinction on marketing résumé. Skilled modern marketers that are at the top of their profession blend the traditional creative skills of marketing with ­technical skills. “Software is marketing’s interface to reality,” says Scott Brinker.[⁷](#A978-1-4842-0259-3_2_Chapter.html#Fn7)

Modern marketers don’t need to know how to use every solution depicted in Figure [2-1](#A978-1-4842-0259-3_2_Chapter.html#Fig1), but there’s a good chance they will use one solution from most of the categories. These solutions enable the capture and tracking of the performance data for an analytics process. The good news for marketers is that the capabilities of these solutions continue to expand while they become easier to use. Still, having a firm grasp on the technology for marketing is no longer a luxury but a necessity, and marketers cannot outsource the technical part of marketing to the IT department. Modern marketing organizations must invest in acquiring and growing their technical capabilities, not just for success with analytics but for all of marketing’s work.

The final challenge that Figure [2-7](#A978-1-4842-0259-3_2_Chapter.html#Fig7) summarizes is identifying and tracking meaningful analytics. Although selected by just 16 percent of participants in the study, it is perhaps the most difficult challenge to overcome in the journey toward becoming data-driven. Once all the other challenges are overcome—systems, resources, staffing, and skills—this challenge remains.

Marketers don’t have to stick their toe very deeply into the analytics pool to understand the seriousness of this challenge. The activities of modern marketing create lots of data. It’s relatively easy to pick something, start tracking it, and make some summary graphs with trend lines headed in what seems like the right direction. The problem is one of relevancy. It’s gratifying, for example, to know that a particular email blast had a higher than average open rate, or to know how many times a social media post is shared. These things are relatively easy to count, but how do they count in terms of creating value? As William Bruce Cameron put it, “not everything that can be counted counts, and not everything that counts can be counted.”[⁸](#A978-1-4842-0259-3_2_Chapter.html#Fn8)

The challenge for marketers is to identify metrics that matter. This process cannot begin with looking for whatever metrics are available or just tracking existing marketing processes. Instead, the metrics identification process has to start with understanding the strategic objectives marketing is pursuing. It is a top-down approach. To do otherwise is putting the horse before the cart. Marketing must determine what metric provides evidence of effectiveness, value, or meaningful outcomes in the pursuit of its objectives.

After identifying the best metrics, the ideal is for those metrics to come directly out of some system in real time and an easy-to-understand, shareable report. Sometimes, this occurs. More often, however, there is no easy, direct path for getting at the best set of metrics for an analytics process. Marketers will find they must derive the right set of metrics from multiple sources, blending and calculating numbers to arrive at something that truly indicates how a process is performing and what value it is producing. This is a process of planning, testing, and refining. It can easily take several reporting cycles to get it right, and even then, it requires periodic validation to determine if the numbers still have meaning.

## Marketing Analytics Capabilities

This chapter has explored the landscape, attitudes, and challenges associated with marketing analytics. Having provided the big picture, we conclude with a brief, high-level discussion of the capabilities and limitations of marketing analytics.

So far in this book, a number of metaphors have represented marketing analytics: dashboards, a servant, guardrails, and so on. These metaphors accurately convey an understanding of what marketing analytics do: provide direction and guidance. When properly implemented, the analytics process emits a steady stream of information that is useful for:

Understanding marketing’s performance. For internal use, the metrics an analytics process uses tell marketing how it is performing. To use the dashboard metaphor, when the gauges are in the green, marketing can continue performing its work with confidence, even accelerating its efforts. When the gauges are in the yellow, it’s an indication that something is about to fail and needs attention. When the gauges are in the red, marketing knows with certainty that something is broken, and the broken process requires either an overhaul or abandonment in favor of something better. Reporting marketing’s performance. For external use, the output of the analytics process, or at least part of it, is useful to show the rest of the organization that it is getting a return on its investment in marketing. Sharing the results of it’s work create confidence in across the organization in what marketing is doing, helping the CMO keep marketing’s seat at the big decisions table.

Analytics deliver these important capabilities to the marketing organization. There are also limitations. Analytics don’t lead. The process is not the functional equivalent of auto-pilot for the marketing team. For the marketing function to thrive and provide the greatest value to the companies it serves, it requires strong, visionary leadership from the CMO. The CMO’s vision for marketing should include an analytics process. The combination of vision and analytics produces the highest performing marketing function that can fully account for its work and sustain its contribution. It represents what every CEO wants from marketing.

Footnotes [1](#A978-1-4842-0259-3_2_Chapter.html#Fn1_source)

“Marketing Report Card: Keeping Our Seat at the Table,” Demand Metric Research Corporation, March 2014\. [http://​www.​demandmetric.​com/​content/​marketing-report-card-benchmark-report](http://www.demandmetric.com/content/marketing-report-card-benchmark-report).

[2](#A978-1-4842-0259-3_2_Chapter.html#Fn2_source)

Ibid.

[3](#A978-1-4842-0259-3_2_Chapter.html#Fn3_source)

“Marketing Analytics in 2013: Benchmarks, Insights & Advice,” Demand Metric Research Corporation, March 2013\. [http://​www.​demandmetric.​com/​content/​marketing-analytics-benchmark-report](http://www.demandmetric.com/content/marketing-analytics-benchmark-report).

[4](#A978-1-4842-0259-3_2_Chapter.html#Fn4_source)

Ibid.

[5](#A978-1-4842-0259-3_2_Chapter.html#Fn5_source)

Ibid.

[6](#A978-1-4842-0259-3_2_Chapter.html#Fn6_source)

Ibid.

[7](#A978-1-4842-0259-3_2_Chapter.html#Fn7_source)

Scott Brinker, The Marketing Technologist: Neo of the Marketing Matrix. [http://​chiefmartec.​com/​2013/​12/​marketing-technologist-neo-marketing-matrix/​](http://chiefmartec.com/2013/12/marketing-technologist-neo-marketing-matrix/).

[8](#A978-1-4842-0259-3_2_Chapter.html#Fn8_source)

William Bruce Cameron, Informal Sociology: A Casual Introduction to Sociological Thinking. New York: Random House, page 13.

# 3. The Impact of Marketing Analytics

Show Me the Money

The first two chapters hopefully made a convincing argument for marketing analytics. It’s not enough, however, to collect all the right data to input into a legitimate marketing analytics process. The key to success with marketing analytics is in the application. If the results and insights from the process are not used, then a marketing analytics initiative is of little value. To benefit the whole organization, the analytics must influence the thinking, the decisions, and ultimately the way marketing works.

To look at marketing analytics as just a performance management tool misses the point of the process. It is true that a proper implementation of analytics can help marketing work better: more efficiently and more effectively. Although the analytics process should provide the data that helps marketing do more with less, ultimately analytics are about making money. The highest and best use of the analytics process is to help marketing operate as a revenue center. When the CEO says, in so many words, “show me the money,” marketing is able to do so when it has the right analytics data and uses it wisely.

Note

Analytics, though useful for improving marketing’s efficiency and effectiveness, are ultimately about helping the marketing function operate as a revenue center.

For marketing organizations that have an analytics process, there are certainly some extremes to avoid. The first extreme is to have an analytics process but then do nothing with the output. The second is to have an analytics process and expect its output to serve as the primary source of leadership for the marketing team—this is the job of the CMO or marketing VP. Analytics are not a substitute or replacement for leadership. The place to situate a marketing analytics process is between these extremes. This is a place where the analytics are real, they have influence, and they serve as a reference point to help the CMO validate decisions and refine strategies.

[Chapter 2](../Text/A978-1-4842-0259-3_2_Chapter.html) presented the state of marketing analytics, along with attitudes about them and the challenges with marketing analytics. How are organizations doing in the real world when it comes to finding this analytics “happy place” between the extremes just described? Figure [3-1](#A978-1-4842-0259-3_3_Chapter.html#Fig1) shares study results[¹](#A978-1-4842-0259-3_3_Chapter.html#Fn1) that reveal what marketing organizations are doing with the analytics data they measure and track.

![A978-1-4842-0259-3_3_Fig1_HTML.jpg](Images/A978-1-4842-0259-3_3_Fig1_HTML.jpg)

Figure 3-1.

Marketing analytics data use

Figure [3-1](#A978-1-4842-0259-3_3_Chapter.html#Fig1) reveals that almost one-fourth of the marketing organizations that have analytics data occupy the first of the two extremes: they make no apparent use of it. For these organizations, analytics is nothing more than busy work. Within the data lies the power to improve performance or at least prove marketing’s contribution, but it goes unused. This lack of use indicates that marketing either feels it is already performing well enough and doesn’t need the boost it would get from using the data, or it doesn’t care.

What’s best is for marketing and the CMO to desire to use analytics as a continuous improvement strategy. In the absence of any pressure from above, the ideal marketing team seeks to better itself and uses analytics as a tool to manage how it works. In the absence of this zeal for improvement, the company executives can also mandate the establishment of metrics and the collection of data around those metrics for the purposes of accountability. Either way, for the 24 percent who do not use the analytics data at their disposal, the accountability doesn’t exist to encourage use of that resource.

For the remaining 76 percent of the sample represented in Figure [3-1](#A978-1-4842-0259-3_3_Chapter.html#Fig1), distributed across four response options, some level of the analytics data use is indicated. None of these remaining responses are negative, although some probably represent higher or more advanced levels of use. Of these remaining four response options, two of them—CMO and marketing team reviews of the data—are internal uses of analytics process output.

Using and reporting analytics data just within the marketing department is not a bad thing. Valid analytics data has the same power to influence actions even if that data never reaches outside the marketing department. In Figure [3-1](#A978-1-4842-0259-3_3_Chapter.html#Fig1), almost half of marketing organizations keep their analytics data within the family. Most common is that members of the marketing team only (38 percent) have access to and use the data. Less common is for the CMO only (10 percent) to see and use the data.

From a macro perspective, one of the primary uses of analytics data is to inform and validate decision making. Even when kept inside the department, the marketing analytics data loses none of its power to do this. However, another primary use of analytics data is as an accountability mechanism. Reporting analytics data outside the marketing department, usually to executives and other stakeholders, establishes a healthy level of accountability. When the data stays within the marketing department, it loses most of its leverage as an accountability mechanism. Lower levels of analytics maturity are commonly associated with keeping the data within the department.

Note

Analytics data is robbed of much of its power as an accountability mechanism when it stays within the marketing department.

The highest level of marketing analytics maturity is associated with organizations that use the analytics data to report performance externally. Figure [3-1](#A978-1-4842-0259-3_3_Chapter.html#Fig1) shows two categories of response that represent external reporting of analytics data: to the executive team and to anyone in the organization. Making up the larger of these groups is the 18 percent of typical marketing organizations that report data to the executive team, and this is the most important level of accountability. When this happens, hopefully one result is an ongoing, healthy dialogue between the CMO and CEO about what marketing is doing and how it is contributing. Analytics data is a wonderful catalyst for these discussions, particularly when the CEO expects this type of accountability and the CMO willingly delivers it.

Constituting the smaller of the two groups that report analytics data externally are the marketing organizations that practice complete transparency. They make the analytics data available to anyone in the organization that wants to review it. This “nothing to hide” attitude about the work of marketing is a commendable position to which many more organizations should aspire but few attain. The marketing teams that practice this level of transparency are generally associated with the highest level of analytics maturity.

## The Impact of Analytics

> Half the money I spend on advertising is wasted; the trouble is I don’t know which half. —John Wanamaker, 19th-century retail pioneer

It’s true that analytics can help marketing improve its performance. With the proper selection of metrics, the collection of data, the diligent analysis and reporting of it, and the resulting decisions, analytics are a potent performance enhancer. But what kind of performance, and how do the improvements come about? If analytics are a means to an end, and that end is better revenue performance, how do analytics get us from here to there?

The end game for marketing analytics is revenue, and with revenue impact and expenses known, it is possible to derive ROI for marketing, a topic of further discussion in [Chapter 6](../Text/A978-1-4842-0259-3_6_Chapter.html). How, then, does the analytics process help create revenue? Understanding how marketing analytics affect revenue is the fundamental starting point for many analytics initiatives.

To understand this, consider the journey that most prospects take toward becoming customers. There is an initial recognition of need, followed by a process of discovering potential solutions, consideration of those solutions, and ultimately a decision. Marketing 101 teaches us that prospects must be aware of solutions so they can consider them and ultimately make a purchase. Much of marketing’s work, therefore, is about creating awareness as early as possible in this buying cycle.

Generating this awareness happens a number of ways. In the earliest days of marketing, there were fewer tools and channels for generating awareness. Marketing and advertising were synonymous, and print was the primary channel. Even then, however, there was a need for analytics data.

John Wanamaker, quoted above, understood the limits that a lack of analytics data about advertising efforts imposed. The only thing he had to help him optimize his advertising was his intuition.

Fast forward to the 21st century, when marketers have at their disposal myriad channels, both traditional and digital, to create awareness. There are so many combinations of messages, creativity, and channels that there is no hope of making intelligent decisions about optimizing the media mix without analytics. They are indispensable to the modern marketer.

The modern marketer’s awareness efforts take the form of campaigns whose desired output is leads: individuals or businesses that have identified themselves as having some level of need or interest in a solution, and who have also surrendered contact information. For most companies, leads are the fuel for the sales engine. It’s marketing’s job to ensure that a steady flow of leads is coming in that are nurtured, qualified, and then passed along to the sales team. Most of the time, the lead generation process is ground zero for marketing analytics.

The right combination of testing and analytics can make the difference between a lead generation campaign that is a flop and one that is stellar. Because marketers don’t have unlimited budgets for lead generation, analytics are an important tool for helping them understand which campaigns to expand, which ones to revise, and which ones to abandon. The analytics provide the information marketers need to optimize campaigns, which in turn generates more leads, which the sales team turns into more revenue.

A wonderful example of how analytics help marketers execute more effective awareness and lead generation campaigns comes from the 2008 U.S. presidential election.[²](#A978-1-4842-0259-3_3_Chapter.html#Fn2) In 2007, then-candidate Barack Obama was competing for the Democratic Party nomination and trailing by double digits in the polls. Dan Siroker, director of analytics for the campaign, recognized that every visitor to the Obama website was an opportunity to raise awareness and campaign funds. The home page of the website featured a large graphic of candidate Obama, and at the bottom of the page was a call-to-action button. The question facing Siroker was, which combination of media and call-to-action buttons would produce the best results?

Siroker set up a series of A/B tests, also called landing page split tests, to determine which combination of six images and four call-to-action buttons was most effective. The images all featured the candidate in various settings. The call-to-action button options were labeled “JOIN US NOW,” “LEARN MORE,” “SIGN UP NOW,” and “SIGN UP.” Siroker’s goal was to determine which of the 24 possible combinations of image and button performed the best, so he set up an experiment to show the various combinations to website visitors and collected the data.

The metric he used to measure success was sign-up rate—the rate for which a test combination of image and call-to-action button caused visitors to provide their email address to the campaign. After gathering enough data, the winning combination was determined, and it was not the combination the staff initially favored.

The page with the top-performing combination had a sign-up rate of 11.6 percent, compared to the original sign-up rate of 8.3 percent. This represents an improvement rate of almost 41 percent. Over the course of the campaign, that difference translated into almost 2.9 million more email sign-ups, with an average donation per sign-up of $21\. The analytics-optimized web page generated an additional $60 million for the campaign.

In this example, analytics made a difference on a grand scale by illuminating critical information about the performance of web landing pages that otherwise would have remained in the dark. There certainly is a cost to taking the approach this example describes: it requires time, effort, and a little bit of technology, and this investment is often a barrier to getting it done. The return on the investment, however, almost always exceeds the cost. The value of being well informed is almost always higher than the cost of remaining ignorant. As Mike Krass, CEO of MKT Media Group, put it, “The price of light is less than the cost of darkness.”

Analytics will certainly help marketers improve their calls to action and campaign landing pages on their websites, but their impact goes beyond that. Analytics can make an impact in these areas as well:

*   Brand recognition: Understand the mindshare your brand enjoys and the sentiments customers have toward it.
*   Content: Know with certainty which of your marketing content is most widely consumed, shared, and produces the best conversion.
*   Channel optimization: Compare performance of various marketing channels, such as email or pay-per-click, to improve their performance or invest only those that perform the best.
*   Customer understanding: Gain a deeper understanding of customer behavior to better understand their needs and preferences.
*   Predictive intelligence: Accurately predict early in the buying cycle which customers will buy and when.

The list represents just some of the areas where analytics can have a significant impact, and not just on the marketing function but across the entire organization. Analytics can only have this impact, however, if they are based on the right metrics, which are diligently tracked and reported and used to influence decision-making, and the organization views the entire process as credible. Even when the analytics process is rock solid, if the organization has no faith in the process, the analytics won’t impact the organization the way they should.

## Analytics Credibility

It’s one thing to have a marketing analytics process at some level of maturity, and quite another to have the basic output of that process—the data—enjoy credibility. Decisions are made on this data, so if the hope is to have confidence in those decisions, the analytics process and the data it produces must be sound. The marketing analytics process has to have integrity and credibility so that the entire organization will have faith in the process and the decisions born out of that process.

Just because a marketing analytics process collects and reports data across a set of metrics doesn’t mean that everyone is going to believe the numbers. Doubt about the accuracy and credibility of the analytics data can exist inside and outside the marketing organization. Sometimes doubt creeps in because the process is suspect, or the input data has quality issues. Other times, the beneficiaries of the data simply don’t want to believe what the data reveals.

It’s not uncommon for anyone to view the data that marketing collects, maintains, and uses as suspect. Much of the data on which marketing relies is often stored in CRM and marketing automation systems. This data finds its way into these systems through a number of paths, and each path presents challenges with respect to maintaining accuracy and quality.

Ideally, an organization will have a process or mechanism for ensuring data quality or cleaning the data, because the longer dirty data is allowed to reside in a system, the higher the probability that quality issues will compromise the analytics, campaigns, people, or other systems that use it. Most organizations, however, don’t have a formal data cleaning process, and the lack of one is at the root of analytics credibility issues. Figure [3-2](#A978-1-4842-0259-3_3_Chapter.html#Fig2) summarizes the state of sales and marketing data quality.[³](#A978-1-4842-0259-3_3_Chapter.html#Fn3)

![A978-1-4842-0259-3_3_Fig2_HTML.jpg](Images/A978-1-4842-0259-3_3_Fig2_HTML.jpg)

Figure 3-2.

Sales and marekting data cleanliness

As Figure [3-2](#A978-1-4842-0259-3_3_Chapter.html#Fig2) depicts, only 36 percent of organizations indicated that their sales and marketing data was “clean” or “very clean.”[⁴](#A978-1-4842-0259-3_3_Chapter.html#Fn4) Since the analytics process is built on the foundation of data, the implication for the other 64 percent is that they are bound to see their analytics process suffer a credibility gap, particularly if it is known or believed that the data quality is low. Even having clean data is no assurance that a marketing analytics process will enjoy credibility.

Still, having clean data is a prerequisite for an effective, credible analytics process. The current data quality status in a majority of organizations should create a sense of urgency for cleaning up this data. This urgency is only heightened when data volume growth is factored in, because for most companies, that volume grows with every sales call, campaign, webinar, or trade show, compounding the problem.

Even if the data volume isn’t growing, static data doesn’t remain valid indefinitely. Data quality decays through contacts changing companies, email addresses, roles, titles, and responsibilities. Marketing analytics data certainly consists of more than prospect or customer contact data, but most of the metrics marketing can track ultimately lead back to the customer, and it is therefore linked to a customer contact record. When it does, organizations need linkages to accurate customer and prospect data. To do nothing about data quality is a default commitment to poor data quality. Organizations must have some level of intentionality and process discipline to even maintain data quality at any level or (ideally) improve it.

A last thought about data quality: regardless of how pristine the input data for the marketing analytics process is, those who are (or would be) critics of the process must understand the level of cleanliness. There’s a public relations burden the marketing team must shoulder in this regard. If marketing is going to preserve or promote the credibility of its analytics process, it must ensure that the data is clean and that everyone knows it is clean. A flawed perception of data quality can sink the credibility of any analytics process.

A lack of faith or credibility in the process, no matter how much care went into selecting and tracking proper metrics, severely hobbles a marketing analytics effort. The seriousness of the credibility issue with analytics is a very real obstacle on the analytics landscape. Figure [3-3](#A978-1-4842-0259-3_3_Chapter.html#Fig3) provides a snapshot[⁵](#A978-1-4842-0259-3_3_Chapter.html#Fn5) of where organizations land on the spectrum of marketing analytics credibility.

![A978-1-4842-0259-3_3_Fig3_HTML.jpg](Images/A978-1-4842-0259-3_3_Fig3_HTML.jpg)

Figure 3-3.

Marketing analytics credibility perception

The data represented in Figure [3-3](#A978-1-4842-0259-3_3_Chapter.html#Fig3) gets to the heart of how much trust exists in the marketing analytics process. Considering how the analytics process should work—producing data that helps marketing keep its efforts aligned with strategy and performing well—a critical success factor is trust. What value is the analytics process if it isn’t trusted enough to influence big decisions? The output from the process, therefore, must influence decision making. It can only have influence if the process stakeholders have confidence in the process and its output.

Currently, just one-third of companies say they attach “good” or “high” credibility to their marketing analytics. Credibility perceptions of “moderate,” “low,” or “no” credibility indicate skepticism. This credibility data reveals that it isn’t enough to have a sound marketing analytics process; the process has to enjoy the confidence of its stakeholders so that analytics can influence decisions. The true litmus test for a marketing analytics process is this: is the company willing to make serious decisions and commit resources based on the guidance the process provides? [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html) detail a plan for helping organizations to implement a sound marketing analytics process, and part of that plan includes efforts to create trust and confidence in the process output.

## Analytics and Decision Making

Ultimately, a marketing analytics process should provide data that facilitates better decision making. As discussed in [Chapter 1](../Text/A978-1-4842-0259-3_1_Chapter.html), analytics exist in the marketing organizations not to make decision making obsolete, but to inform decisions. After decisions are made, analytics have another use: to determine whether those decisions were the right ones. The most mature marketing analytics processes use analytics in both the pre– and post–decision-making phases.

To understand how analytics can inform and then validate decisions, let’s walk through a hypothetical decision-making situation for a marketing team confronted with a challenge. Imagine that you are the CMO. During a meeting of the executive team, the chief financial officer (CFO) reports that revenue projections for the quarter show a “miss” by half a million dollars. After some discussion, the CEO turns to you, the CMO, with a charge to figure out how to make up the projected revenue shortfall.

If you were in a marketing organization that did not use analytics or didn’t use them much what would you do? You would still make decisions, and you would use your experience and intuition. Sometimes, this approach would serve the organization well, but other times it wouldn’t. It’s a roll of the dice, and in situations like these you wish an analytics process was already in place to provide some insight.

After the meeting with the CEO, your first stop is the VP of sales. Together, the two of you must determine where the revenue will come from: new customers, existing ones, or a combination of both? Because the company has implemented CRM and marketing automation systems, the metrics you need to make a wise decision are available. What you know is that the average sales cycle to take a new, qualified prospect to close is six months. Furthermore, the analytics tell you that your sales team successfully converts into a sale 30 percent of qualified prospects with which you engage. With an average sale of $5,000, you’ll need 100 new sales to close the revenue gap. Given your sales conversion rate, the marketing team will need to identify 330 new, qualified prospects to get $500,000 in new revenue.

Here’s the problem: you don’t have time to generate 330 new, qualified leads, and take at least 100 of them through the six-month sales cycle before the end of this quarter. Therefore, you continue your deliberations with the VP of sales by reviewing the opportunities in the sales pipeline that are already in process. Again, because you use the proper systems—in this case, CRM—you have the data available.

Working with the VP, you analyze the pipeline data, removing the leads that are already projected to convert this quarter. After doing this, you discover is that there are 285 qualified leads languishing in the sales pipeline. These are opportunities that entered the pipeline as qualified leads, have gone through part of the buying cycle already, but for various reasons have not warranted the attention of the sales team or have they been nurtured by marketing.

You and the VP determine these 285 existing qualified leads represent the best opportunity to make up the $500,000 revenue shortfall by the end of the quarter. One concern is that the average conversion rate tells you that you can expect to turn 30 percent of these leads into sales. At that conversion rate, with just 285 leads you’ll gain about 85 sales or $425,000, falling short of filling the revenue gap by $75,000. Past experience has taught you that taking the same approaches produces the same results. You think out loud for a few minutes about trying some new approaches to nurture these neglected leads in the pipeline. Leaving the VP’s office, you pledge to put your team to work on a plan to convert these 285 at a higher rate.

Once back in your office, you gather your team and discuss the challenge. Typically, your lead nurturing efforts consist of emails linked to landing pages that offer some sort of premium content, such as a white paper or case study. Your team has developed a reputation for developing compelling content that is also visually appealing. The Achilles heel, however, has been getting prospects to the landing pages to get this content, which is “gated” or kept behind a form requiring the visitor to surrender their contact information.

With your team, you review the results of several previous email campaigns. For each campaign, you have analytics data that includes open rates, bounce rates, click-through rates and even which recipients opened a message. As you review this data, you confirm what you thought was correct: your open rates have historically been strong. This is a benefit of efforts you make to ensure the quality of the data in your CRM and marketing automation systems. What the analytics also show, however, is that the click-rates are lower than industry averages, and they are declining. In other words, the response to your promotional email has been strong, but not enough recipients are clicking on the links in the email to the landing pages you’ve created. This is troubling, but at least the analytics are telling you where the problem is.

The next thing you and the team review is the web analytics data for the landing pages. Here you discover another problem. Too few of the email recipients that are taking the time to click through to the landing pages are choosing the call-to-action button, usually something like “Download Case Study” or “Sign Up Now” and similar calls to action. This data confirms what your instincts have suggested for a few months: you’re experiencing eroding performance of the tried-and-true nurturing methods that have served you well. Until now, you’ve never had a projected revenue shortfall to create a sense of urgency about monitoring these analytics more closely.

As you and the team ponder the implications of what the analytics are telling you, there is discussion about how to nurture these 285 leads differently, in a way that hopefully produces a conversion rate in excess of 30 percent. If you all can put together a campaign that produces a 35 percent conversion rate, you’ll get the 100 sales needed to generate the half million in revenue. The arithmetic is straightforward. The big questions is, what approach will do this?

A member of your team has recently been experimenting with interactive video, conducting some A/B testing on various landing page configurations. Interactive video is marketing video content that features calls-to-action links built right into the video itself. The videos are usually less than two minutes in duration, and when it ends, the viewer is presented with one or more call-to-action buttons. In a departure from how you typically use landing pages, the team tested interactive video that was not “gated”—in other words, visitors to the landing page did not have to first put in their contact information. Instead, at the end of the video, one of the calls to action allowed the viewer to request further information and contact from a sales representative.

Offering ungated content is a departure from the method your team has used in the past. Web forms have always been part of your campaign landing pages because they allow you to capture leads for the sales team. However, this case is different. You’re not trying to capture leads but accelerate the buying cycle for leads you already have. The analytics from the interactive video testing look very promising: visitors clearly seem to prefer video and they are opting to click the “Contact Me” button at the end of the video at twice the rate that you’ve experience for other forms of content. You make the decision to build a campaign using interactive video.

The next big campaign decision is to determine what the video will feature. Video marketing best practices indicate that videos of 90 seconds or shorter are the most successful. Again, you and your team review analytics data to determine what type of marketing messages might work best for qualified prospects who are later in the buying stage. Analyzing past campaigns, you’ve seen the strongest conversion rates for content that features a case study, a real-world story of a customer that implemented your solution with a description of the benefits they received. Based on the available data, you put the team to work producing a 90-second interactive case study video.

The team works quickly, and the video is ready to deploy in short order. Using your marketing automation system, you prepare the campaign landing page and the email that will go out to the 285 prospects you’ve identified. Using the analytics data in your marketing automation system, you identify the ideal day of the week and time to send the email.

Because you have created a data-driven culture in the marketing team, they are always thinking about how to measure everything they do. This campaign video was created with the ability to track viewing duration and shares. Furthermore, your marketing automation system is fully integrated with the CRM system, so when a qualified prospect views the video, that activity is logged in both systems. In addition, when a prospect views the entire video, an email alert is sent to a member of the sales team. Your campaign will track and report these metrics in real time, allowing the sales team to respond quickly when a prospect’s behavior indicates interest.

You launch the campaign. The first few days after the initial email send will tell you if your strategy is working, or if you need to develop a Plan B. The tracking data on the email open rate shows that it is within the normal range, as you expected. What’s most encouraging, however, is that the click-through rate to the campaign landing page is slightly above average. Even better, the landing page call to action to view the case study video is performing much better than previous landing page invitations to consume gated content. So far, the strategy is working beautifully, but the ultimate campaign metric—revenue—is still unknown.

It’s time to check in with the VP of sales. You stop by for a quick update and find the sales bullpen is buzzing! The VP has little time to chat, but for the best of reasons. The interactive video is generating a flurry of inquiries to the sales team through the calls to action options embedded at the end of the video. The case study video is clearly resonating. The VP shares that a number of prospects have resumed a sales dialogue with words to the effect of “we’re having the same problem as the customer in the video—help us solve it.” The sales team is booking demonstrations and having quality interactions with many of the prospects targeted in the campaign.

Within a matter of weeks, the quarterly revenue forecast is revised upward. The VP of sales reports that an additional 105 deals are forecast to close by the end of the quarter, erasing the projected revenue shortfall. You and the marketing team debrief about the campaign, reviewing all the metrics to determine how to improve tactics the next time. One thing everyone agrees on: when you approach the CFO for additional funds to invest more heavily in video marketing, you won’t have any difficulty getting approval given the results of this campaign.

As the preceding scenario illustrates, analytics influence the decision making that takes place within a modern marketing organization in many ways, both large and small. This scenario represents just one way analytics help drive confident decisions whose results you can link directly to revenue.

Footnotes [1](#A978-1-4842-0259-3_3_Chapter.html#Fn1_source)

“Marketing Analytics in 2013: Benchmarks, Insights & Advice,” Demand Metric Research Corporation, March 2013\. [`http://www.demandmetric.com/content/marketing-analytics-benchmark-report`](http://www.demandmetric.com/content/marketing-analytics-benchmark-report)

[2](#A978-1-4842-0259-3_3_Chapter.html#Fn2_source)

“How Obama Raised $60 Million by Running a Simple Experiment,” Optimizely blog, November 29, 2010.

[`http://blog.optimizely.com/2010/11/29/how-obama-raised-60-million-by-running-a-simple-experiment/`](http://blog.optimizely.com/2010/11/29/how-obama-raised-60-million-by-running-a-simple-experiment/)

[3](#A978-1-4842-0259-3_3_Chapter.html#Fn3_source)

“Sales & Marketing Data Quality Benchmark Report,” Demand Metric Research Corporation, November 2013\. [`http://www.demandmetric.com/content/sales-marketing-data-quality-benchmark-report`](http://www.demandmetric.com/content/sales-marketing-data-quality-benchmark-report)

[4](#A978-1-4842-0259-3_3_Chapter.html#Fn4_source)

Ibid.

[5](#A978-1-4842-0259-3_3_Chapter.html#Fn5_source)

“Marketing Analytics in 2013: Benchmarks, Insights & Advice,” Demand Metric Research Corporation, March 2013\. [`http://www.demandmetric.com/content/marketing-analytics-benchmark-report`](http://www.demandmetric.com/content/marketing-analytics-benchmark-report)

# 4. The Marketing Analytics Process

Lather, Rinse, Repeat

> If you can’t describe what you are doing as a process, you don’t know what you’re doing. —W. Edwards Deming

Businesses are very process driven. There are processes for sales, support, accounting, manufacturing, hiring, training, and virtually everything a business does. Even in our personal lives, we use processes constantly. Although we don’t give much thought to it, most of us probably shower the same way: lathering, rinsing and toweling off using the same sequence day after day. From the smallest tasks to the largest projects, processes are the way we get things done.

Processes help us work efficiently and effectively. When we realize that a certain task or activity has an element of repetition to it, and when we’re trying to achieve consistent outcomes, we develop a process for it. Once a process that works is identified, we stick with it, making improvements as we use it. In some companies, these processes are quite formalized and intentionally managed to optimize performance. There may be designated process owners who constantly evaluate the performance of their processes, looking for ways to rid them of inefficiencies and help them perform better. In other cases, the processes are informal and no one pays much attention to them as long as they work. Either way, processes are how things get done inside organizations.

Marketing is a macro process or system, consisting of many subprocesses. This view might seem to make marketing sound too machine-like, leaving creativity out of the picture. That’s not the intent of putting marketing in the context of process: the creative process is vital to marketing’s success, and it is capable of measurement, management, and improvement.

So why all this talk about processes? Processes require management to perform at peak effectiveness. You can’t manage a process that you can’t measure, so measurements—analytics—are essentially to managing marketing and all the processes of which it consists. Marketing analytics itself is a core or foundational process within marketing. Let’s revisit the definition of marketing analytics first shared in [Chapter 1](../Text/A978-1-4842-0259-3_1_Chapter.html):

> Marketing analytics is the process of identifying metrics that are valid indicators of marketing’s performance in pursuit of its objectives, tracking those metrics over time, and using the results to improve how marketing does it work.

The marketing analytics process should serve as the performance interface to all other marketing processes. For everything that marketing does—email marketing, content marketing, lead generation, events marketing, pay-per-click advertising—there are ideally analytics associated with the processes. The marketing analytics process calls for identifying meaningful performance metrics for all marketing processes, tracking those metrics, analyzing them, and taking improvement actions based on the analysis.

Note

The marketing analytics process calls for identifying meaningful performance metrics for all marketing processes, tracking those metrics, analyzing them, and taking improvement actions based on the analysis.

The analytics process sounds simple enough, but it is often grand in scope considering all the things that marketing does. Consider just some of the metrics associated with the email marketing process: open rate, bounce rate, click-through rate, unsubscribe rate, and conversion. These metrics are multiplied by the number of email messages sent by a marketing organization, and it’s easy to see how quickly the marketing team is swimming in data. For this reason, the marketing analytics process is not incidental to the work of marketing, but integral to it. The dashboard metaphor for analytics is accurate: analytics provide the performance information for marketing leaders to drive the marketing machine at peak efficiency, producing the best possible results.

There’s an old joke about the instructions on a bottle of shampoo: lather, rinse, and repeat. If you follow those instructions literally, you would find yourself in an infinite process loop, until you ran out of shampoo. When it comes to marketing analytics, however, we want to interpret those instructions literally, making a continuous cycle of measure, analyze, and improve. There’s limited value in going through the process cycle only one time. Marketing analytics is most effective when there is the discipline to repeat the cycle continuously. The balance of this chapter will review the analytics process in greater detail.

Note

The marketing analytics process is a continuous cycle of measure, analyze and improve.

## Step 1: Identify Metrics

> Metrics aren’t a form of control—they monitor progress, provide direction, and remove blindfolds. —Christel Quek (via LinkedIn)

In any endeavor, marketing or otherwise, measurements are critically important because they are the means of judging success or failure. Marketing is often considered an artistic endeavor, and as such, many don’t believe its true impact is measurable. There’s no denying the artistic and creative aspect of marketing. In fact, great creative talent and work is a major differentiator between merely good and truly great marketing efforts. But just because marketing is one of the most creative business disciplines should not exempt it from measurement. In this modern era, the debate about measuring marketing’s results has been settled in the affirmative.

The fact is that in most organizations, marketing’s raison d’être is to produce revenue, either directly or indirectly. Although it is true that marketing has other missions, such as serving as guardian of the brand, if it is not a key variable in the revenue equation, most CEOs will dismiss marketing as a failure. So measurement is very important. The first challenge of measuring marketing is determining which metrics to use. This is no small matter, for the success of the entire analytics process hinges on the proper selection of metrics.

In this discussion of marketing metrics, it’s helpful to consider sales, a part of the organization with a long history of being metrics-driven. The relationship between marketing and sales is close one from a mission and goals perspective. Both functions are tasked with generating revenue, but they go about it very differently. The measurement of sales results is usually pretty straightforward. Revenue goals are set, quotas assigned, and sales are tracked, quite often to the individual representative responsible. It’s not difficult, particularly in B2B sales organizations, to know who has contributed the most to attaining revenue. The relationship between sales activity and revenue is very direct and thus fairly easy to measure.

It’s not always this clear in marketing. The reason isn’t because marketing has no effect on revenue but instead because the path to revenue for marketing’s activities is more indirect. It’s simply harder to connect the dots between the work marketing does and the results it produces. This indirect relationship between marketing’s output and revenue creates challenges to identify meaningful, relevant metrics. It’s not an impossible task, but it requires careful deliberation and selection of metrics.

One of the challenges in identifying the best set of metrics for the marketing analytics process is the sheer number of metrics linked to the work of marketing. The average marketing organization can actually produce volumes of data about most of its work, but not all of that data provides insights into real results, nor does it have meaning outside of the marketing organization.

If numbers are what the organization wants, marketing can provide them. Hundreds of metrics are available. Social media gives us likes, shares, posts, tweets, and other metrics. Email marketing generates opens, bounces, click-throughs, unsubscribes, and more. Websites let us track visits, unique visits, referral sources, search terms, and much more. There are conversion rates, content downloads, views, and a seemingly endless stream of metrics at the disposal of the modern marketing team.

Each metric has potential value in helping marketing derive its revenue impact, but almost none of them are a direct tie to a revenue figure. It’s certainly expedient to simply track data that is easy to collect and then report it, particularly when there is pressure to show some accountability. It’s unwise, however, for marketing to simply shove its full collection of metrics at the organization to satisfy the need for accountability. Although the myriad available metrics show evidence of activity, they don’t really answer the question about how marketing is performing. Marketing must be selective about which metrics it chooses, sticking with the most relevant ones so that viewing a portfolio of metrics doesn’t feel like drinking from a fire hose.

Another challenge to selecting metrics for the analytics process is that so many of them don’t have much meaning outside of the marketing department. This doesn’t mean they are useless, but it does infer that if marketing chooses to use metrics not well understood by nonmarketers, there is an obligation to explain what they mean and why they’re important.

For example, consider an impression. Marketers understand that impressions are simply a count or measure of exposure to some form of content, traditional or digital. Measuring impressions is a pretty common metric for many things marketing does, such as various forms of advertising. Marketing is always interested in garnering as many impressions as possible, and digital impressions are fairly easy to track. Imagine this scenario: a CMO gets budget approval to run a digital marketing campaign. The campaign performs well, compared to similar, previous campaigns, producing 15 percent more impressions than expected. The marketing team is elated at this result.

Should the CFO ask how the campaign went, it’s very easy for the marketing team to report the impressions data. But not all CFOs or CEOs understand exactly what an impression is, nor do they understand that not all impressions are of equal value. Furthermore, they’re not terribly interested in impressions anyway: they want to know what those impressions translate to in terms of revenue.

Impressions data from a campaign can help the marketing team improve the performance of existing and future campaigns. If the performance is low, marketing should investigate why; usually the problem is that the messaging is wrong, the medium is wrong, or both. Analyzing the data can help the team improve campaign performance or replicate great performance. As H. James Harrington said, “Measurement is the first step that leads to control and eventually improvement.”[¹](#A978-1-4842-0259-3_4_Chapter.html#Fn1)

As the campaign impressions example illustrates, impressions aren’t useless as a metric, but they don’t tell the story the C-suite longs to hear and marketing should want to know: the revenue impact of the campaign. To be fair, most marketers understand the need to translate metrics like impressions, visits, downloads, and so on into revenue. The problem is that they often don’t have the systems to collect the necessary data to do this with precision. Of course marketing can always estimate, taking the actual number of impressions and applying some historical, believed-to-be-accurate conversion rate and derive a figure of revenue generated. But as soon as they does this, it has left the realm of certainty and entered the realm of speculation, and it exposes itself to credibility issues as a result.

A final challenge worth discussing is the difficulty of finding metrics that measure things that are important. For example, take the concept of brand equity, an asset that is grown through marketing. By definition, brand equity is the mindshare and recognition that a brand enjoys and the benefits that come with that. Brands with a lot of equity can charge premium prices, enjoy a more loyal following, and have a degree of immunity from competitive threats. Yet brand equity is difficult to measure. Organizations that measure this often use some combination of metrics, such as market share, unaided recall, Net Promoter Score, and referrals. There is rarely a dispute about the importance of growing brand equity, but measuring it is another matter entirely. As a result, it makes the business case for campaigns designed solely to increase brand equity more difficult to prepare and justify.

These challenges—the sheer number of potential metrics, the fact that many of them don’t relate directly to revenue, and the reality that some important things are difficult to measure—seem to imply that identifying metrics is like navigating a minefield. It doesn’t have to be like this. When determining how to best measure the results of marketing’s efforts, follow these guidelines:

*   Focus on objectives
*   Measure efficiency
*   Measure effectiveness

Marketing’s set of metrics must measure progress toward objectives, or they are measuring the wrong things. The ideal source of these objectives is the marketing strategy, and proper objectives are always time-bound and measurable. This strategy and supporting objectives are hopefully aligned with the company’s vision and business objectives. If this is the case, then identifying metrics is done by determining how to measure progress toward those objectives. When there is a marketing strategy with defined objectives, the objectives make selecting the right metrics pretty straightforward.

To illustrate this concept, consider as an example a very common business objective: to maximize profits. The marketing team develops a strategy to help maximize profits and develops two objectives:

Increase market penetration of current products in current markets. Improve customer retention.

The marketing team plans and executes a series of campaigns and activities to achieve these objectives. Because both of these goals are measurable, marketing should have no difficulty understanding if the things it is doing are “moving the needle” in the right direction. For the first objective, marketing could measure market share, new customer acquisitions, and other things to know what progress it’s making. Likewise, marketing can measure customer retention, satisfaction, referrals, reorders, and other metrics to understand retention and the impact it has on revenue. It isn’t difficult to understand the revenue impact of what marketing is doing in either of these areas.

Identifying meaningful metrics is very difficult if there is no marketing strategy or set of related objectives. In the absence of a marketing strategy, where do the business and marketing objectives come from? While it is possible to arbitrarily choose some objectives to pursue, and then identify some metrics that go with them, that isn’t really a strategic approach to marketing. There is a symbiotic relationship between the marketing strategy (and its objectives) and marketing analytics. The marketing strategy should preexist the analytics process, and the objectives of that strategy should serve as the basis for identifying metrics for the analytics process.

So far, this chapter has driven home the need to have metrics that relate to marketing’s revenue performance. What’s also necessary are some metrics about efficiency that show how well the marketing team is getting its work done. These are essentially productivity measures, and most of the time, they’re for internal use, helping the CMO keep the marketing engine humming. Marketing should have some efficiency metrics that help it understand how well it is getting work done, as long as they are not the only type of metric the analytics process uses.

It’s very easy for efficiency metrics to become “vanity” metrics, because they help marketing feel good about how much work is getting done. What these metrics don’t do is tell you whether you’re working on the right things. When you’re using efficiency metrics, it’s important to remember that it is possible that it could show that you’re doing a wrong thing very well.

Because efficiency metrics only tell marketers if they’re doing things right, another type of metric is needed that helps marketers know if they’re doing the right things. These are effectiveness metrics, and they help marketing measure its impact. Examples of effectiveness metrics would include conversion rates, a behavioral indicator that means a prospect voluntarily took the desired action that moves them further down the sales funnel. Although this and similar metrics don’t always mean a sale is imminent, it’s a step toward a possible sale. It’s far easier to connect effectiveness metrics to revenue.

Identifying metrics is the critical first step in the marketing analytics process. Without the proper metrics, the remaining steps in this process are of little value. Generally, marketers that are new to analytics will identify a core set of metrics that are refined over time and expanded through experience.

### Step 2: Analyze the Metrics

> The goal is to turn data into information, and information into insight. —Carly Fiorina, Information: The Currency of the Digital Age

The second step in the analytics process is analysis: taking the data and, through inspection and analysis, turning it into actionable information. The goal of this analysis step is to draw insights from the data about marketing’s progress toward achieving its objectives. These insights aren’t always obvious, so analysis and interpretation helps them come to light.

During the first step, we identified the metrics that are the crucial inputs to the marketing analytics process. With the proper metrics identified, the analysis step begins by capturing and tracking these metrics. In this modern era, where almost all marketing is digital, this means having the right systems and mechanisms are in place for reliably tracking metrics and making them available for analysis.

The place where many marketing organizations were first introduced to analytics is through their websites. The website is one of the most important marketing channels for a company and capturing analytics data about website performance is imperative. Even for bricks-and-mortar businesses, a website is often the first stop for consumers who are looking for information as they consider a purchase. Every website should have analytics enabled for it so that marketers can understand performance and optimize the company’s web presence. Google Analytics is the ubiquitous, free solution most marketers use for this purpose. The realm of marketing analytics, however, extends far beyond web metrics.

If the right systems are in place to capture metrics, understanding the current state of things is not difficult. The modern marketing organization uses three core systems to capture the analytics data it needs: web analytics, CRM, and marketing automation. There are, of course, other systems that provide data to marketing as well, such as purchase transaction data, but these three systems are the pillars. Without the data collection mechanisms these key systems provide, an analytics process is dead in the water. There is simply too much going on in the modern marketing organization to track metrics data without the benefit of automation and technology.

With the right systems in place, a marketer can quickly get a snapshot of current performance metrics. In just a few clicks, one can see how many unique visitors have hit a campaign landing page, how many emails were opened, how many clicks a pay-per-click (PPC)scheme generated and more. Most marketing organizations organize their metrics into some sort of dashboard to visually present the data, making it easier to communicate and understand. [Chapter 7](../Text/A978-1-4842-0259-3_7_Chapter.html) discusses the use of dashboards in the marketing analytics process in greater detail.

When analyzing marketing metrics, what marketers should do is understand the current state, compare it to the ideal state, and then do root-cause determination to explain any differences.

Note

The analysis step of the marketing analytics process begins with understanding the current state, comparing it to the ideal state, and understanding why there are differences.

Seeing the current state of marketing’s performance by reviewing the latest analytics data is interesting for sure, but it’s just the beginning of the analysis process. The first question that reviewing this data prompts is usually: are these results good? Knowing whether any particular measurement result is good, bad, or indifferent requires some sort of comparison, to historical data or a set of benchmarks that represent a standard of excellence, preferably the latter.

Comparison is important because without it, the metrics provide no sense of direction. Consider the very important metric of customer satisfaction. An initial, quantitative assessment of customer satisfaction has limited usefulness. To illustrate this, suppose a survey of customer satisfaction is conducted using a scale from 1 to 5 where 1 equals very satisfied, and 5 equals very dissatisfied. This survey result reveals that the average satisfaction level is 2.3\. How should the organization feel about this? This result is on the “satisfied” end of the scale, so there is some comfort in that, but this single snapshot of customer satisfaction says nothing about where this key metric is going.

What’s ideal is to have the data from the previous measurement period for comparison; even more useful is to have historical data to understand the trend over time. If we’re looking at landing page conversion rates, how have they performed historically? The same is true for almost any marketing metric. When there is improvement from one measurement period to the next, why did it occur? Likewise, when there is degradation from one measurement period to the next, it’s important to investigate to identify the reason. The root causes of changes in metrics are not always easy to identify. Analyzing the data is an exercise that sometimes is akin to detective work.

Note

Effective analysis requires an objective perspective and a determination to constantly pursue explanations for the results marketing produces.

Benchmark data from external sources isn’t always available, but when it is, it provides a useful comparison. For example, webinar benchmark data tells us that the ratio of registrants to attendees averages 42.9 percent.[²](#A978-1-4842-0259-3_4_Chapter.html#Fn2) Less than half of the people who register for webinars actually attend them. With this data, companies that produce webinars can know if their registration-to-attendee ratio is cause for alarm or celebration, and they can do something about it if they don’t like how their figures compare to the benchmark.

This analysis step of the process is about drawing insights from the data about marketing’s effectiveness. Analyzing all the metrics marketing collects about its work is important because it is the way we understand marketing’s performance. It is analogous to monitoring the gauges on the dashboard of a car. When a gauge goes into the red, we understand there is a problem, but we don’t necessarily know what the problem is. The gauge gives us some idea of where to begin looking for the source of the problem. The diagnosis and prescription is the subject of the next section of this chapter.

The analysis of the marketing metrics provides a barometer of marketing’s performance. Because this analysis provides the basis for taking improvement actions, it is very important that the data used for analysis be accurate and complete. It’s possible to do excellent analysis on bad or suspect data. The marketing analytics process requires accurate, complete data, and attempting to execute the process without it will lead to erroneous conclusions and ineffective attempts to improve what marketing is doing. When this happens, as it sometimes does in the zeal to become analytics driven, it damages marketing’s reputation and credibility. Marketers must ensure that the data for the analytics process is accurate and reliable.

### Step 3: Take Improvement Actions

Excellent firms don’t believe in excellence—only in constant improvement and constant change.

—Tom Peters, In Search of Excellence

Analyzing the marketing metrics tells us what is happening and why, but it doesn’t always tell us what to do about it if the results don’t meet expectations. Nor does the analysis always help us know how to replicate what is happening when outcomes exceed expectations. What marketing must do in this step of the analytics process is determine what actions and changes are most likely to yield improvement. No matter how thorough the analysis, it is of no value if it doesn’t lead to some sort of improvement action.

Sometimes, the improvement actions are obvious. For example, analysis of a mass email send that performs poorly reveals that an unusually high number of email addresses bounced back, indicating they were invalid. The improvement action here is obvious: remove the invalid addresses and keep the email contact list current. Improvement actions aren’t always so obvious in other cases.

In another scenario, a PPC ad campaign doesn’t perform well. Comparing this underperforming PPC campaign to previous campaigns shows the click-through rate (CTR) from the landing page is well below the norm. The analysis shows that the same strategy was in use for all PPC campaigns: a unique landing web page was set up for those who clicked on the ad. The offer or “call to action” conversion on the landing page was similar to previous campaigns that did perform well. The traffic to the landing page was within expected parameters. So what’s wrong, and more important, what is the right improvement action?

In this scenario, there’s clearly some problem with the landing page, but the analysis doesn’t tell us how to fix it; it only tells us where the problems seems to reside. In this example, it is the landing page itself, because it is not ­producing the conversions at a rate similar to previous campaigns. To find a solution, the marketing team could perform some A/B testing, a process referenced briefly in [Chapter 3](../Text/A978-1-4842-0259-3_3_Chapter.html).

A/B testing involves presenting two versions of a landing page to visitors. The A version is the control page and the B version has some variation. The variations might include different call-to-action text, graphics, a different page layout, color scheme, or any conceivable change. The goal is to find what variations perform the best. Both pages are presented to visitors, and the analytics are monitored closely. The visitors themselves determine the winning page. In this example, the winning page is the one with the best conversion rate. It is likely that a series of A/B tests are needed to fully optimize a campaign.

A/B and other kinds of testing and research are very helpful tools in determining which improvement actions marketing should take. Testing and research help eliminate the guesswork from improvement actions, but too few marketers take the time to do it. The enemy of the testing and research approach is expediency and the mind-set of “we’ve always done it this way.” It takes time and discipline to test, research, and determine what to do as a result. Marketers under a lot of pressure to produce immediate results are often reluctant to do market research and test approaches before committing to them, as it simply takes too much time. But it’s time well spent, because it helps ensure that improvement actions are the right ones the first time. Testing and research, therefore, are excellent initial steps to identifying improvement actions.

Of all the steps in the analytics process, the improvement step is the least scripted. The range of potential improvement actions is limited only by the creativity of the team tasked with identifying them. When searching for improvement actions to take, brainstorm all possible ideas. Don’t reject any ideas in the beginning. Then, before implementing improvement actions, narrow the list of candidate ideas down to one if possible. Too many simultaneous improvements will make it difficult or impossible to know which one contributed the most to better performance.

Are improvements always necessary? Is it ever possible to skip the improvement step because things are going so well? In theory, any process always has room for improvement. In practicality, there is a point of diminishing returns on improving something marketing is doing. If there are performance objectives or standards for marketing tasks or activities, and the metrics analysis indicates that marketing consistently meets or exceeds them, then marketing can look elsewhere to invest its time and improvement efforts. Keep in mind that the scope of what marketing should measure is broad, so success in one area doesn’t mean it’s all right to ignore the entire analytics process. It just means that the area where success is occurring, based on the metrics analysis, frees marketing to investigate and improve elsewhere.

This improvement step isn’t just about addressing low performance. It’s also about replicating what’s working well. The analysis step will identify excellent performance, and when it does, the marketing team should determine how to apply what caused it to future efforts. The causes of ideal outcomes form the basis of best practices that help the organization consistently achieve the desired results.

### Do It Again

> You go back, Jack, do it again. —Steely Dan, “Do It Again”

The three-step process described in this chapter represents how the marketing analytics process should be executed. Each organization will have a unique variation of the process, with different metrics, different conclusions from the analysis, and different improvement actions to take. Yet the three steps remain constant: measure, analyze, and improve.

Executing this process just once or intermittently will produce some benefit, but its greatest value comes from continuous use. Repetition of the analytics process is necessary to produce consistent and sustained improvement. The real advantages of analytics only come with ongoing, long-term execution of the process. Every time marketing does work in pursuit of its objectives, that work should fall within the scope of the analytics process.

For every marketing campaign, communication, event, and investment, the cycle of measure, analyze, and improve should occur at the conclusion before the next campaign, communication, event, or investment takes place. The frequency of invoking the analytics process will of course vary depending on what marketing is doing. Some marketing actions are so important that analytics are monitored in real time, whereas others require daily, weekly, or monthly scrutiny.

What’s important is to not let complacency infiltrate the process. It’s tempting to relax when regular reviews of the metrics show that everything is fine. In fact, in these circumstances, it is very easy to assume that marketing’s work is so fine-tuned that it can run on auto-pilot. This state of marketing nirvana, is rare, and when it does materialize, it never lasts for long. Remaining vigilant to the analytics process is the way to ensure the best long-term performance of marketing.

Footnotes [1](#A978-1-4842-0259-3_4_Chapter.html#Fn1_source)

Joseph L. Levy, “In My Opinion,” CIO Enterprise, September 15, 1999, p. 10.

[2](#A978-1-4842-0259-3_4_Chapter.html#Fn2_source)

ON24 Webinar Benchmarks Report, 2014 edition. [`http://www.on24.com/wp-content/uploads/2014/04/ON24_Benchmark_2014_Final.pdf`](http://www.on24.com/wp-content/uploads/2014/04/ON24_Benchmark_2014_Final.pdf) .

# 5. Getting Started with Analytics

Ready, Set, Go!

> Marketing has always been a grueling and competitive sport—not unlike running a marathon. With the changes in the buying process, in media and technology, and managing expectations, it’s like running a marathon as the ground shifts beneath your feet. What was already difficult is becoming increasingly difficult. If you’re going to do it without measurement, it’s like running a marathon, in an earthquake, blindfolded. [¹](#A978-1-4842-0259-3_5_Chapter.html#Fn1) —David Raab

The critical part of any journey is the first step. Taking the first step in the marketing analytics journey represents a willingness, commitment, and desire to become a data-driven, revenue-oriented marketing organization. But it takes more than just desire. Success with marketing analytics requires a plan that encompasses resources, preparations, potential hazards one might encounter along the way, and contingencies. This chapter provides the framework for developing such a plan.

This chapter assumes that the reader is part of an organization that has no analytics process in place and is preparing to begin this journey. As Figure [2-4](../Text/A978-1-4842-0259-3_2_Chapter.html#Fig4) revealed, just over a quarter of marketing organizations fall into this category. Other organizations that are further along can still benefit from the information this chapter shares, using it to help benchmark and assess their own process maturity.

This journey has a beginning, but no end. As [Chapter 4](../Text/A978-1-4842-0259-3_4_Chapter.html) shared, the marketing analytics process is a continuous loop. There are, however, some preparatory steps for those who are just starting the journey and checkpoints for those already on it. These milestones include assessing organizational readiness, studying business objectives, determining or clarifying marketing objectives, and establishing the right set of metrics. The remainder of this chapter explores these milestones in greater detail.

Note

The major milestones in the marketing analytics journey include assessing organizational readiness, reviewing or clarifying business objectives, and establishing the right set of metrics.

## Assessing Organizational Readiness

> Most of us spend too much time on what is urgent and not enough time on what is important. —Stephen Covey, The Seven Habits of Highly Effective People

If it were possible to observe how organizations with a mature marketing analytics process operate, you would notice a certain characteristic: they don’t let urgent matters get in the way of what’s important. To these organizations, analytics are very important, not just in marketing but in all business areas. This kind of operational philosophy is actually quite strategic. It’s a reliable indicator that the organization is committed to a plan that is well understood, one that is metrics-enabled, of course.

As organizations embark on the marketing analytics journey, they must first assess their readiness to do so. This path isn’t just about adding another à la carte process to the organizational menu. Instead, for analytics to effect the change and improvement of which they’re capable, organizations must weave analytics into the very fabric of their operational being. For this reason, the first thing to do when preparing to implement a marketing analytics process is to see if the organization is ready to make the journey. The initial areas to assess are the leadership and culture of the organization.

Success with marketing analytics is the product of several characteristics, the first of which is culture, and culture is a function of leadership. If you’re trying to set out on this journey, will your organizational culture allow you to succeed? Even when leadership is providing encouragement and saying “yes” to analytics, the culture of the organization can still represent a barrier. Table [5-1](#A978-1-4842-0259-3_5_Chapter.html#Tab1) provides a contrast of cultures as they relate to marketing analytics success.

Table 5-1.

Cultures that Act as a Barrier and as a Catalyst for Marketing Analytics Success

| Culture as a Barrier | Culture as a Catalyst |
| --- | --- |
| Work on what’s urgent (reactive) | Work on what’s important (proactive) |
| Punish mistakes | Learn from mistakes |
| Trust intuition | Trust data |
| Political environment | Transparent environment |

Culture is a deal-breaker for many corporate initiatives. A healthy culture is a greenhouse for marketing analytics; a dysfunctional culture a desert wasteland. The attributes in Table [5-1](#A978-1-4842-0259-3_5_Chapter.html#Tab1) represent the end points on a cultural and leadership readiness spectrum for marketing analytics. You should assess where your company is on this cultural spectrum so that an analytics initiative doesn’t shrivel up and die due to a hostile cultural environment.

Organizations with some or all of their cultural attributes in the “barrier” column must understand that the environment for success with marketing analytics may not exist. The irony for these groups is that their dysfunctional cultures indicate a desperate need for the change that marketing analytics can help bring about. Yet, their cultures are a hostile environment for the marketing analytics process and the accountability it brings.

What must happen in such organizations for marketing analytics to succeed? The culture must change, moving toward the attributes listed in the “catalyst” column of Table [5-1](#A978-1-4842-0259-3_5_Chapter.html#Tab1). Such change, however, doesn’t come easily. Cultural change usually requires existing leadership to change, adopting and modeling new attitudes, or for new leadership to replace existing leadership. Either way, cultural change doesn’t occur quickly and it’s rarely painless. Still, marketing leaders should be agents for change in such organizations, advocating for cultural evolution (not revolution) that will create benefits far beyond opening the door for marketing analytics.

Does marketing analytics have any chance for success in organizations where the culture is a barrier? In fact, it does. Cultural evolution and marketing analytics can occur in parallel. Although the cultures in these organizations may not support a formal marketing analytics initiative, it can often get started in “stealth” mode. Without fanfare or even official sanction, it’s possible to start small, have some success, and then carefully expand the scope of marketing analytics. The idea is simple: create some success, and it becomes very hard for the organization to deny the value of the effort. A series of small, real marketing analytics successes can help propel cultural change.

Organizations with some or all of catalyst cultural attributes can proceed with the confidence of knowing that their environment is ideal for a marketing analytics initiative.

Skills represent the second area to assess. There are four critical skill sets necessary for success with marketing analytics: analytical ability, technical ability, insight or understanding, and communications. It is rare to find single individuals with each of these four key skills in abundance, but all of these skills must be present in the organization that wishes to pursue marketing analytics.

Note

The four critical skill areas for marketing analytics are analytical ability, technical ability, insight or understanding, and communications.

The first skill requirement is perhaps the most obvious: analytical ability. This ability really represents a hunger to investigate the data to learn what it can reveal and the statistical and mathematical skills to build and work with data sets and models. Someone who is analytical by nature can learn the mathematical and modeling skills. However, someone who has no taste for analytical work will struggle with it, even if they learn the skills.

What kind of analytical capability must a person or organization have to succeed in marketing analytics? Initially, the requirements aren’t very rigorous. Organizations just getting started with marketing analytics will find that tracking and analyzing basic metrics doesn’t require advanced analytical skills. It’s more simply a matter of having the discipline to do the work, which can amount to little more than reviewing the data. In the early going, much of the analytics work is as simple as capturing the data, reviewing it, and determining what to do as a result.

As the organization’s marketing analytics maturity grows, so will it’s proficiency. The progression and maturation of the marketing analytics process will lead to the need to use basic, applied statistical techniques to examine not just individual metrics but the relationship between some of them. Techniques like cross-tabulation and correlation analysis can provide a deeper understanding of the analytics data, helping explain how a change somewhere in one part of the marketing ecosystem might ripple through to affect an outcome in another part. Experience with statistical tools like SAS or SPSS can help the analyst perform this deeper analysis.

Developing a deep understanding of customer behavior has long been the Holy Grail of marketing and a marketing application of Big Data analytics. Gaining valuable insights about customers and their behavior requires marketers to exploit a wide variety and high volume of customer transaction data that is both structured and unstructured. Advanced mathematical and analytical skills are required to build the data sets and models associated with Big Data and predictive analytics. [Chapter 10](../Text/A978-1-4842-0259-3_10_Chapter.html) will cover Big Data in greater detail. The insights available through Big Data and predictive analytics are game-changing for many companies. The advanced skills necessary to do this type of analytics work represents the most mature end of the marketing analytics spectrum, one that takes some time and experience to reach.

The second skill requirement area is technical ability: proficiency retrieving data and using analysis tools on it. This could be considered an IT skill set. The data a marketing analytics process uses as input is usually found in various systems throughout an enterprise. There isn’t a “system of record” for marketing analytics that houses all the data the process might need. Marketers will need technical skills to retrieve the data from the places where it is stored, and the technical requirements to do this range from very simple to complex.

Some data is retrieved through easily structured queries into systems that house it, using a simple graphical interface to get the job done. Other systems require database programming skills, such as the use of Structured Query Language (SQL) to retrieve what is needed out of databases where it is stored. As the marketing analytics process matures, it is highly likely that data from several sources will need to be combined into a single database to analyze it, draw important insights from it, and report results. Doing this often requires the use of a business intelligence tool. Part of the marketing analytics readiness assessment must therefore consider if marketing has access to the technical skills required to support the process.

The analytical and technical skills are often found in the same person; even when they’re not, someone who is strong in one of these skills is predisposed to learning the other one. This skill combination, however, is often not found on the marketing team, and this must change, not just for the sake of analytics but for all marketing work, which is increasingly technology-based or -driven. As Scott Brinker, author of the Chiefmartec.com blog puts it, “You must be the driver of marketing technology, not merely a concerned passenger.”[²](#A978-1-4842-0259-3_5_Chapter.html#Fn2)

The third skill requirement to assess is the ability to understand what the marketing analytics mean and what to do about it. Perhaps the best way to describe this is as insight. The analytical and technical skills will yield a result as an output of the process, but without the capability to draw insights from these results, the marketing analytics process will disappoint. The skill of drawing insights out of the analysis is often the result of experience, inquisitiveness, outside-the-box thinking, and the ability to see the big picture. Someone who is good at making the complex seem simple and who can see the inferences in the results will often have this skill.

This skill area is difficult to describe and even more difficult to measure. What kind of people have these skills? They are generally those who have the capacity to process a lot of input, view it objectively, draw inferences from it, and see what isn’t obvious. They have excellent critical thinking skills and are not prone to jumping to conclusions. People who have this kind of insight are able to look beyond the numbers and discern what the numbers mean. Their ability to understand the implications of the marketing analytics process output is crucial to the effort. Some may argue that what is described here isn’t a skill at all but in inborn talent. Whatever it is, having this capability is important to the success of a marketing analytics effort.

Communications are the fourth and final skill area to assess. Marketing can build amazingly accurate mathematical models of their work, perform detailed analysis of its marketing analytics data, and draw insightful conclusions about the implications of it all, but get no benefit from it if the results aren’t communicated well. The communication that must occur goes far beyond simply distributing reports. What is needed is a communicator who can speak with authority and conviction about the insights gained through the marketing analytics process.

The communications that must occur around the marketing analytics process is not mere reporting of results. (If that were the case, then a simple email message would do). Instead, what is needed is a leader who can objectively convey what is going on as reflected by the analytics, helping the rest of the organization not just understand it but believe in it. The communications that must occur are formal and informal. The person communicating on behalf of the marketing analytics process must master the board room presentation to the executives as well as the break room conversation around the water cooler.

Part of the skill in communicating in this context is knowing the level of detail to convey. Marketing analytics can certainly consist of a lot of detail. Some will need to understand the intricacies of the process, whereas others will just want the results summary. The person doing the communications about the process will need to understand at all times who the audience is and what their information needs are. To use a metaphor, the executive who asks “what time is it?” doesn’t want to hear a set of instructions for building a watch as a reply. It’s easy for someone who is intimately familiar with the analytics process to overcommunicate about it, but that usually won’t win people over. A person who is adept at communicating about marketing analytics understands how to convey meaning succinctly while providing detail when it is helpful to do so.

The person who communicates about marketing analytics must also be a teacher and evangelist for the process. This requires the discernment to know who doesn’t understand the value and results of the process and then educate them. In a similar way, the communicator is also always promoting the process, ensuring that the organization understands how the results help not just the marketing function but the entire organization. The communicator is therefore a leader, teacher, persuader, defender, promoter, and advocate of the marketing analytics process.

Finally, the communicator must understand the necessity of being transparent. The organization can’t selectively choose to only communicate when the analytics indicate that marketing is producing good results. The communication must occur regardless of what the analytics show about marketing’s performance. This is the accountability dimension of the analytics process, and marketing cannot shy away from it or censor its output if the process is to work. The better marketing becomes at using analytics, the more frequently it will have good results to report. Even when it doesn’t, the right corporate culture will respect the marketing team for its transparency.

As with the first two skills requirements—analytical and technical—it is also quite possible that the insight and communications skills are found in the same person. Unlike the first two skills, these last two skills are often found within the marketing team. What is rare, however, is for a single person to possess all these critical skills. For this reason, marketing analytics is best viewed as a team endeavor, with several people coming together with the necessary skills under the direction of a leader with a vision for using analytics.

Although analytic and technical abilities are hard skills and insight and communications are soft skills, they are equally important. Table [5-2](#A978-1-4842-0259-3_5_Chapter.html#Tab2) provides a simple assessment mechanism for rating current skills required for analytics. Use it to take inventory and assess the current skills available in your organization for marketing analytics. Place a check mark in the column for each listed skill that represents your current state, and develop a plan for addressing any deficiencies.

Table 5-2.

Marketing Analytics Critical Skills Inventory/Assessment

| Skill | Proficient | Cursory | Absent | Unknown |
| --- | --- | --- | --- | --- |
| Analytical skills |   |   |   |   |
| Technical skills |   |   |   |   |
| Insight |   |   |   |   |
| Communication |   |   |   |   |

The next major area to assess is data. Do you have access to the data you need to support the metrics you’ve defined for your marketing analytics process? The challenge is that there really is no “system of record” that contains all that marketing will need to support a robust analytics process. [Chapter 4](../Text/A978-1-4842-0259-3_4_Chapter.html) discussed the process of identifying metrics for this process. It’s important to have the metrics identified first, so that the availability of data doesn’t overly influence what gets measured. In other words, don’t let the tail wag the dog: don’t measure what you can measure because you have the data available. Measure what you should measure, and figure out how to get the data to do so.

To assess if you have the data you need as input for your marketing analytics process, first study the metrics you’ve selected for tracking, analysis, and reporting. What data is required for these metrics? Does it exist? If so, where? If it doesn’t exist, is it obtainable? What effort is required to have access to accurate, reliable data needed for your marketing analytics program? Your data availability assessment must answer these questions.

Although there is no single system of record for marketing, some systems provide much of the necessary data, at least for the basic metrics. For example, an organization that is executing a digital marketing strategy and also using a marketing automation solution will find that much of the data it needs to measure landing page visits or clicks on call-to-action buttons (conversions) is captured automatically and easily accessible. Likewise, almost anything an organization needs to know about its website performance is available through the use of Google Analytics. Data for most of the basic sales activity metrics, web metrics, and digital marketing metrics are available in various systems such as customer relationship management (CRM), marketing automation, or web analytics systems.

As the marketing analytics process matures, metrics become more sophisticated, and they are often derived from multiple data sources. For example, calculating the cost of new customer acquisition may require data from marketing automation and accounting systems. Likewise, determining the conversion rate from qualified leads to sales for a campaign may require access to CRM and web analytics. In general, the more informative metrics, particularly the ones that show a link to revenue, are often derived from multiple data sources. If the data exists in some sort of system or database, it is often possible to automate the extraction and derivation of the metric and present it in some sort of report or dashboard. The use of tools for this purpose is discussed further in [Chapter 8](../Text/A978-1-4842-0259-3_8_Chapter.html).

It’s always possible that the metrics the marketing organization wishes to use are not available in any existing system or source of data. When this is the case, the organization will have to determine how to get the data it needs. For example, suppose that marketing determines some sort of advocacy metric is important to track as part of the analytics process. It may decide to use Net Promoter Score[³](#A978-1-4842-0259-3_5_Chapter.html#Fn3) (NPS) and therefore will implement some sort of surveying mechanism to gather the NPS data. Other metrics may also require the implementation of systems, processes, or both to collect the necessary data. Your data assessment should consider where the data could come from and what it will take to collect it.

Table [5-3](#A978-1-4842-0259-3_5_Chapter.html#Tab3) provides a template for doing an assessment of the data your marketing analytics process requires. To use it, list each metric in the leftmost column, and under the appropriate data columns, briefly describe the source of the data. If the data does not exist (rightmost column), describe any options for obtaining it. When complete, develop a plan to address the metrics for which data does not currently exist. If the degree of difficulty of obtaining the data is too high or it’s too costly to obtain, you may need to reconsider how practical the associated metric is.

Table 5-3.

Marketing Analytics Data Inventory/Assessment

| Metric | Data Exists | Data Derivable | Data Nonexistent |
| --- | --- | --- | --- |
| Example: email opens | Marketing automation |   |   |
| Example: conversion rate |   | CRM, marketing automation |   |
| Example: Net Promoter Score |   |   | Develop NPS survey process |
| Metric 1 |   |   |   |
| Metric N |   |   |   |

The final area of assessment is the budget. Are funds allocated to support the startup of a marketing analytics process and for sustaining it once it is in place? Resources are required to reach success with marketing analytics. The importance of completing the assessment suggested here is to understand the level of investment the process will need to work as it should. Once the funding requirement is understood, make sure the organization is willing to commit to it before proceeding.

When planning the marketing analytics budget, some of the places to invest may include some or all of the following:

*   Staffing: additional headcount to properly own and administer the marketing analytics process. It’s likely that additional staff are needed for the first two skill areas discussed previously: analytical and technical abilities.
*   Systems: the necessary systems for collecting, tracking, analyzing, and reporting on data needed as input to the marketing analytics process.
*   Training & and education: even if the staff is available, the skills may not be. Training and education are often needed to equip the team that will manage the marketing analytics process, and to provide some level of continuing education.
*   Outside consulting, services or expertise: the fastest path to effectiveness in marketing analytics may go through outside expertise. Consulting and services can often help accelerate the implementation and success of the process.

Marketing analytics isn’t an expensive endeavor, but it requires some funding if it is to have impact. Figure [2-5](../Text/A978-1-4842-0259-3_2_Chapter.html#Fig5) summarized data collected from more than 700 executives and marketing professionals about the percent of their budget allocated to analytics. As that graph and the related discussion in [Chapter 2](../Text/A978-1-4842-0259-3_2_Chapter.html) reveals, the return on marketing analytics is proportional to the investment made. Those organizations allocating the greatest portion of their marketing budget to analytics are claiming the get the most from their efforts.

What is the right level of investment in marketing analytics? As [Chapter 2](../Text/A978-1-4842-0259-3_2_Chapter.html) discussed, that level appears between 6 and 10 percent of the marketing budget. This level is a guideline. Common sense should prevail when it comes to funding marketing analytics, making sure that the funding is adequate to enable the success of the process.

## Reviewing Objectives

> Objectives are not fate; they are direction. They are not commands; they are commitments. They do not determine the future; they are means to mobilize the resources and energies of the business for the making of the future. —Peter Drucker, Management

Marketing’s work should support corporate objectives. When developed the right way, marketing’s strategy and objectives are built from a deep understanding of corporate goals and objectives. Marketing’s purpose, is after all, to help the organization succeed in pursuit of those objectives.

Marketing analytics should directly reflect the organization’s marketing strategy, and less directly the corporate objectives on which that strategy is based. For this reason, an important part of getting started with marketing analytics is to determine, refine, or clarify both sets of objectives: marketing’s and the corporation’s. These objective must remain in clear focus as the marketing team is putting together its own strategy and the analytics that measure how well it is working. A clearly discernible thread of continuity should run from the marketing analytics process through the marketing strategy to the corporate goals and objectives.

Marketing analytics implementation planning should begin with a review of corporate goals or objectives, which are typically stated at a fairly high level. Corporate objectives represent what the organization wishes to accomplish. The marketing strategy is an expression of how to meet those objectives, and analytics are then the success indicators.

For example, a common business or corporate objective is to improve profitability. All departments within the company should determine what strategy they should execute in pursuit of this and all other corporate objectives. Marketing may determine that it will pursue two strategies to improve profitability: increase market penetration of current solutions in established markets and improve customer retention. Marketing will then plan its work around these two elements of strategy, each of them cascading into multiple tactics or projects.

Of course, each initiative should have some metrics associated with it. When the marketing initiatives and metrics are developed from the top down as described here, the organization has some level of assurance that there is alignment between the top-level corporate objectives and the things marketing is doing. Having alignment or continuity between corporate objectives and marketing’s work is imperative. One way to ensure it exists is to start the strategy planning process by using the corporate objectives as input and keeping those objectives in mind throughout.

Although starting the marketing planning and analytics process with corporate objectives doesn’t guarantee success, it does ensure you’re oriented properly. This approach, as Stephen Covey describes it, “begins with the end in mind.” The chances that the ensuing strategy and metrics are the right ones are much higher.

This planning process of deriving the marketing strategy from corporate objectives is not difficult to understand, and not too difficult to complete assuming that a clearly articulated set of corporate objectives exists. What if there are no stated corporate goals and objectives? Is marketing just to develop its strategy and related analytics in a vacuum? What is marketing to do in this situation?

It’s a reality that not every organization’s leadership understands the value of intentionally developing and then communicating the top-level objectives to the employees. It’s not that such objectives don’t exist—they often do inside the head of the leader—it’s that they’ve never been communicated well (or at all). The reasons for this lack of communication vary: failure to see the need, lack of leadership or communication skills, or just the assumption that they’re obvious to everyone and therefore don’t need communicating. Whatever the reason, in this situation marketing must do its best to discern the corporate objectives before setting a strategy in place. The solution is often as simple as sitting down with the CEO for a discussion of objectives.

The thing to keep in mind as you start a marketing analytics process: always make sure that the analytics track and measure the execution of the marketing strategy, and that the marketing strategy is based on corporate goals and objectives.

## Establishing Metrics

> Measuring busy-ness is far easier than measuring business. —Seth Godin

All the planning for marketing analytics eventually comes down to selecting appropriate metrics, putting a process in place to measure and track them, and then doing something with the data.

When you’re just getting started with analytics, it’s not wise to put every conceivable marketing metric in place. Instead, start with a few of the most relevant metrics and build the portfolio up over time as you gain proficiency with the analytics process. In fact, what is likely to happen is that the initial set of metrics you select will lead you to other metrics you’ll want to track as well.

What is the right set of metrics for a marketing analytics process? The answer depends entirely on an organization’s marketing strategy. As discussed in the previous section, the best or right set of metrics are derived from the marketing strategy. There are some categories of metrics that are commonly used in the marketing analytics process. These include metrics for the marketing process, customers, lead generation, and social/digital marketing.

Note

Common categories of marketing metrics include measures of the marketing process, customers, lead generation, and digital marketing.

Your portfolio of metrics should include some that indicate how productive the marketing team is. As previously cautioned, these can easily become “vanity” metrics, but when they’re used properly, they help the team understand how efficiently it is working. Example metrics in this category include assets created, impressions generated, year-to-date budget status or ROI, a subject covered in detail in [Chapter 6](../Text/A978-1-4842-0259-3_6_Chapter.html). The right metrics in this category are those that help the CMO and the marketing team know how well it is getting its work done.

The metrics portfolio should also include some customer metrics. These metrics serve as the vital signs for customers and are one of the most important marketing metrics categories. A number of important metrics fall into this category, including customer lifetime value (CLV), customer satisfaction, customer retention rates, Net Promoter Score (NPS), cost of new customer acquisition, and customer churn rates. Almost all of these metrics are tremendously valuable when it comes to understanding the economics of marketing’s work and its impact on revenue.

Any business needs a steady stream of new prospects or leads to generate sales and grow. The metrics associated with lead generation activities are a very important part of many marketing analytics efforts. In fact, lead generation is the primary activity for many marketing teams, and because much of the work of lead generation is digital, there’s a rich set of data available for collection and analysis. Performance of lead generation processes is tracked a number of ways, including new leads by channel (e.g., email marketing, social media marketing, mobile marketing), new leads by campaign, lead flow or volume, new opportunities created, qualified leads created, conversion rates, cost per lead/qualified lead, and of course revenue generated.

The final category of metrics is broad and expansive: digital marketing. Within this category, there are sets of metrics for measuring the growing list of digital marketing channels. Some of these digital marketing metrics are:

*   Website: traffic sources, most visited pages, search ranking, visits or unique visits, time of site or page, bounce rate, and exit pages are all potentially important for measuring website performance.
*   Social media: social network reach (followers, fans, subscribers, or contacts), shares/retweets, engagement, posts, and referral traffic. Your social media metrics should include your blog.
*   Email marketing: database or list size, number of sends, open rate, click-through rate, and bounces are all standard metrics for email marketing.
*   Digital advertising: this includes pay-per-click and other forms of paid, digital media. Key metrics could include impressions, click-throughs, inquiries, landing page views, conversion rate, opportunities identified, revenue generated, and program ROI.

What is presented here is just a sampling of the metrics available to support a marketing analytics program. Each organization will have a slightly different portfolio to support its process. The right ones measure marketing’s success in the execution of its strategy and show real results. Organizations that are getting started with marketing analytics should select just a few to launch their process; as they mature, they will want to have metrics for each of the categories described here.

Footnotes [1](#A978-1-4842-0259-3_5_Chapter.html#Fn1_source)

David Raab, “Winning the Marketing Measurement Marathon,” September 2010, [http://​www.​marketo.​com/​_​assets/​uploads/​Winning-the-Measurement-Marathon-final.​pdf](http://www.marketo.com/_assets/uploads/Winning-the-Measurement-Marathon-final.pdf).

[2](#A978-1-4842-0259-3_5_Chapter.html#Fn2_source)

Scott Brinker, “Rise of the Marketing Technologist,” Chiefmartec.com blog, April 18, 2010, [`http://chiefmartec.com/2010/04/rise-of-the-marketing-technologist/`](http://chiefmartec.com/2010/04/rise-of-the-marketing-technologist/) .

[3](#A978-1-4842-0259-3_5_Chapter.html#Fn3_source)

See [`http://www.netpromoter.com/why-net-promoter/know/`](http://www.netpromoter.com/why-net-promoter/know/) .

# 6. Return on Investment

The Ultimate Metric?

For many marketers, proving the return on investment (ROI) for marketing has long been a thorn in their flesh. Those outside of the marketing ­department are uniformly in favor of having marketing report its ROI. They want the same level of ROI accountability for marketing as for every other department. Those within the department tend to have mixed opinions. Some ­marketers embrace ROI calculation and reporting as the best means of ­validating ­marketing’s work. They recognize that having the ability to accurately report on marketing’s ROI creates acceptance, and if the return meets or exceeds expectations, it makes justifying budget and resources much simpler. ROI has the potential to depoliticize marketing funding and operations.

Others within the marketing team fear the expectation to measure their ROI because it could reveal low performance and expose them to repercussions. Others simply think that focusing on ROI compromises marketing’s true ­mission. Whatever the stance, marketing has been slow to embrace ROI as a metric, and as cartoonist Brady Bonus illustrates in Figure [6-1](#A978-1-4842-0259-3_6_Chapter.html#Fig1), it’s still the exception for ROI to be part of the marketing conversation.

![A978-1-4842-0259-3_6_Fig1_HTML.jpg](Images/A978-1-4842-0259-3_6_Fig1_HTML.jpg)

Figure 6-1.

For most marketing organizations, understanding its ROI remains elusive

Of the challenges marketers face, proving ROI is the top challenge, ranked well ahead of securing budget.[¹](#A978-1-4842-0259-3_6_Chapter.html#Fn1) Regardless of whether marketers love or hate being subjected to ROI scrutiny, many members of the C-suite expect to see these numbers, so marketing should understand what it is and how to report it.

One problem with marketing ROI is that many marketers simply don’t understand what it is, as this Quora post[²](#A978-1-4842-0259-3_6_Chapter.html#Fn2) illustrates:

*   Question: “What is considered a good ROI for a digital marketing campaign for a consultancy company?”
*   Answer: “If you can generate sales leads for under $50 you're probably going pretty well. Especially since your contracts are probably in the thousands. A response rate of 20 percent for ‘soft conversions’ like ebook downloads would be considered good as well.”

The response to this post, though helpful, never gets around to stating anything about ROI, although it provides some data that might help in its determination.

ROI is a profitability ratio that measures the profit returned from each ­investment, with its results expressed as a percentage. Businesses use ROI as a success measure for various investments and projects. It is used to evaluate past successes as well as forecast future ones. The formula for calculating ROI is fairly simple:

![$$ \mathrm{Return}\kern0.5em \mathrm{on}\kern0.5em \mathrm{Investment}\kern0.5em =\kern0.5em \frac{\mathrm{Gain}\kern0.5em \mathrm{on}\kern0.5em \mathrm{investment}\kern0.5em \hbox{--} \kern0.5em \mathrm{Cost}\kern0.5em \mathrm{of}\kern0.5em \mathrm{investment}}{\mathrm{Cost}\kern0.5em \mathrm{of}\kern0.5em \mathrm{investment}} $$](Images/A978-1-4842-0259-3_6_Chapter_TeX2GIF_Equa.gif)

In the calculation, the cost component is not hard to come by. The “gain” element of this equation is difficult to measure with a degree of precision that creates confidence in the result.

To illustrate, consider the example of attending a trade show, a marketing activity that often requires justification and reporting of subsequent ROI. The assumption in this example is that revenue from sales will ultimately result that is attributable to participation in the show. The ROI calculation seems quite simple, accounting for the hard costs and revenue, as Table [6-1](#A978-1-4842-0259-3_6_Chapter.html#Tab1) delineates.

Table 6-1.

Example of Determining Trade Show ROI

| Description | Cost | Gain |
| --- | --- | --- |
| Exhibit/sponsorship fees | $15,000 | — |
| Transportation fees | $1,500 | — |
| Travel expenses | $2,500 | — |
| Promotional item expense | $1,000 | — |
| Literature & misc. expense | $1,000 | — |
| Total costs | $21,000 | — |
| Sales from show leads | — | $45,000 |
| Total return |   | $24,000 |
| ROI |   | 114% |

The example in Table [6-1](#A978-1-4842-0259-3_6_Chapter.html#Tab1) makes determining ROI for participation in a trade show seem simple, and in fact, with the 114 percent ROI shown here, the return on this investment is fantastic. What this scenario doesn’t account for, however, are the soft benefits that are very real but difficult to account for. There is increased brand awareness that occurs, an enhanced reputation as an industry thought leader, competitive intelligence gathered, and new product ideas that came from face-to-face interaction with trade show attendees. None of these benefits are accounted for in the ROI calculation shown in Table [6-1](#A978-1-4842-0259-3_6_Chapter.html#Tab1), and their ultimate value could far exceed the $45,000 in sales in this example. Perhaps accounting for these soft benefits is not important when the hard costs and benefits deliver a 114 percent ROI, but when the business case is break-even or shows a loss, then quantifying the soft benefits becomes critical, yet it’s hard to do.

Despite these challenges, measuring the ROI of marketing is politically smart. Marketers cannot adopt an elitist attitude that puts them above the accountability of ROI measurement just because they feel they are a mysterious force for good that defies measurement. No other department in an organization gets a pass on meeting ROI expectations. Why should the marketing department be the exception?

The reality is this: measuring marketing’s ROI with a high degree of certainty is a serious challenge in most organizations, and some marketers feel it is impossible. What is helping make it possible are the improved systems, technologies, and the ease of using them to capture the data essential to determining ROI. However, there isn’t a system that can capture all that marketing does to create value. Even if the data were available to precisely calculate the ROI of marketing, some in the industry feel it isn’t always appropriate, and others feel it is just plain wrong.

How do you translate a better customer experience into ROI? The systems that marketers use are getting better, as is their discipline at using them, but you still can’t translate everything that marketing does into an ROI measurement. As an illustration of this difficulty, Scott Monty, executive vice president of strategy at SHIFT Communications once said, “What’s the ROI of putting your pants on in the morning?”[³](#A978-1-4842-0259-3_6_Chapter.html#Fn3) It’s difficult to know the answer with certainty, but easy to imagine the negative consequences of failing to do so. So it is with marketing: it must do some of the things it does because it just makes sense to do them—the metaphorical putting on pants in the morning—and it will therefore make no sense to capture the ROI of everything marketing does.

James Archer, founder and CEO of digital, brand, and communication design firm Forty, takes an even stronger position against what he calls the metric-lust of the marketing and advertising industry: “As technology has given our industry the ability to measure more things faster than ever, there’s been a steady trend towards having metrics serve as the basis for design and ­creative work, with the idea that this will ensure return on investment (ROI) for ­marketing efforts. It’s safe. It’s predictable. It’s simple. It’s easy to show your boss. It’s easy to show the client. It makes everyone look and feel good. And it’s wrong.”[⁴](#A978-1-4842-0259-3_6_Chapter.html#Fn4) There isn’t universal agreement even within the industry about measuring ROI. Some think it is imperative, others think it’s benign, and still others think it actually causes harm.

Some of the things marketers do lend themselves to simple and easy data collection and ROI derivation. For example, any website activity generated by a marketing campaign is something the average marketer can use to smother the organization with data and credibly talk about the ROI. Attributing ­revenue to lead generation activities, a major responsibility for many marketing ­organizations, is reasonably simple. With the revenue and expenses known, determining the ROI of any digital campaign is usually simple arithmetic.

Brand building and awareness, another responsibility everyone agrees is important, also falls to the marketing department. Brand equity is not so ­easily measured, and even if it were, its effect on revenue is hard to fully understand. To illustrate, imagine a group of soccer parents, because of the amazing customer experience you engineered for them, raving about your company or product on the sidelines during the kids’ match. This sentiment toward the brand is wonderful, but there’s just no way to capture that sentiment data easily. The organization will probably benefit from the advocacy, but determining its ROI just isn’t possible today, and if it were, there are probably all kinds of privacy considerations involved in doing so.

The modern marketer still faces a dilemma when it comes to what kind of posture to maintain regarding ROI. Here are some of the cultural and practical realities of ROI measurement in the marketing department:

*   Anecdotes about marketing’s performance are inadequate substitutes for ROI data. When pressed, marketers are adept at pulling colorful and inspiring anecdotes out of their experience that help illustrate the value and effectiveness of marketing. This anecdotal information is often illustrative, but it’s not an acceptable substitute for real ROI information.
*   Anecdotal information is sometimes very useful. When valid ROI data about marketing is presented, anecdotes are very effective for helping create context and perspective, and they add color to the data.
*   When times are good, no one is giving much thought to marketing’s ROI. There are times in the life of most firms when everything is going right. New sales records are set every month, “Product of the Year” or “Best Place to Work” awards are won, and glowing reviews from customers and media flow in. In an environment such as this, not many in the firm are thinking about the ROI of marketing. Everyone just makes an assumption that marketing is doing a stellar job, and usually it is! “If it ain’t broke, don’t fix it” is the prevailing attitude. In times like these, marketing is given a free pass on reporting ROI, but they’re foolish to take it if it is offered.
*   When times are hard, the spotlight is shining on marketing, and scrutiny of its ROI becomes a corporate pastime. The judgmental question marketing gets from all corners of the firm is “What have you done for us lately?” The firm is suffering a revenue drought, and everyone looks to marketing to help make it rain. The difficult ­discussions and tough questions about marketing expense are ­inescapable, because through some amazing transformation, most people in the firm have become marketing subject matter experts. Without ROI data about marketing’s performance, everyone assumes the worst. Not only will marketing’s reputation suffer, but its headcount often does as well.

Every firm has a unique personality and culture, and this affects how they ­perceive the return on their marketing investment. Firms with dominant finance or engineering cultures often don’t “get it” when it comes to the value of marketing. Their data-oriented cultures make it difficult to accept the intangible benefits marketing provides, so it gets no credit for them. Marketing equals “fluff” in these cultures, and getting respect, yet alone budget, is more challenging. Without ROI data to prove that marketing is helping generate revenue, it is difficult for marketing to help these companies succeed. Often when it does help, the credit goes elsewhere.

Firms that have sales and marketing cultures are much more likely to “get it” when it comes to the value of marketing. Firms with these cultures place a high value on marketing, and they have an almost intuitive understanding that marketing is the oil in the sales engine—even if you can’t see it, you know it’s there doing its job, and the engine won’t run without it. ROI is helpful, but for these firms, marketing is an imperative that will exist in some form regardless of the existence of proven ROI.

For some organizations, ROI is quite possibly the most important metric for their marketing analytics process. For others, it’s neither a fit nor a ­requirement. It’s never safe to assume that ROI is a must-have metric or key performance indicator for everyone. To assume that it is puts the cart before the horse. What is more important than achieving objectives? As discussed in [Chapter 4](../Text/A978-1-4842-0259-3_4_Chapter.html), part of the analytics process is to understand the business objectives, execute a marketing strategy that helps achieve them, and select metrics that provide indicators of success in that endeavor. Isn’t it possible that marketing can achieve its objectives without using ROI as one of its metrics? The honest answer is “yes.” It’s not always a requirement to have ROI in the metrics portfolio to guide marketing in its work, but the rest of the organization may not see it this way.

Marketers have to keep the proper perspective about ROI. It should never become the tail that wags the dog. At the same time, marketers can’t be cavalier about ROI, because there are times when the ability to demonstrate a return keeps the marketing function intact. One of those times is when management hands the marketing department a mandate to slash its spending. The marketing department that can show the kind of return it consistently delivers has the ability to defend its spending, perhaps even expand it if it truly functions as a revenue center.

When troubled economic times hit a company, CEOs and CFOs are often compelled to cut expenses. Most experienced marketers know that frequently the first place the executives look to cut costs is in the marketing department. However unfair the characterization, many in the organization, including the CEO, assume that marketing harbors waste and excess. When the ROI isn’t measured and reported, marketing is a victim of this characterization. Measuring ROI, therefore, can provide marketing with a shield to help deflect attempts to gut marketing during rounds of budget cuts.

So how should marketers feel about reporting the ROI of their work, and what stance should they take on this matter, which is a source of some interdepartmental friction in many firms? The primary pursuit of marketing isn’t producing a return, it is helping the business achieve its objectives. Although there is almost always a return associated with doing that, when marketing organizations start to obsess about ROI, they can lose their focus. As Gordon Wyner, executive vice president at Millward Brown says, “Linking marketing to financial performance is not now—nor has it ever really been—solely about return on investment (ROI). Quantifying the return achieved by marketing activities is a necessary step in the process of connecting marketing to finance, but is not, in itself, sufficient for understanding how marketing helps to achieve business objectives or how its contribution can be improved.”[⁵](#A978-1-4842-0259-3_6_Chapter.html#Fn5)

Marketing’s focus should remain firmly fixed on achieving business ­objectives. When marketing properly derives its own objectives from the business ­objectives and measures progress toward achieving them with relevant metrics, it’s not always necessary to force ROI into the metrics portfolio. Sometimes, it will make great sense to include ROI as a metric. Other times, using ROI as a metric won’t really help marketing execute its plan more effectively. One of the greatest shortcomings of ROI as a metric is it’s inability to diagnose the root cause of low ROI. In this sense, it’s a bit like a “check engine” light on the marketing vehicle dashboard that only tells you there’s a problem somewhere, but without telling you where. For this reason, knowing the ROI of marketing doesn’t directly help marketers improve their effectiveness.

At the same time, marketers have to remain politically astute. There are ­situations, economic times and company cultural influences that make ­reporting ROI a necessity. If ROI is the yardstick that the CEO and CFO use to measure departmental contribution and determine funding, then ­marketing must participate. This is particularly true in firms that have organizational silos, where one department or silo views another department as ­competition for resources. In such environments, ROI may be used as a lever to gain an advantage over another department in negotiations for resources and favor.

In truth, there’s a healthy level of competition that should exist between departments within companies because it spawns innovation and efficiency. In companies with strong, healthy cultures, this kind of rivalry benefits everyone. In companies with dysfunctional or unhealthy cultures—particularly where that dysfunction manifests itself as organizational silos—the ROI metric can become a weapon, not just to advance one department’s agenda but to harm another.

So is ROI the ultimate metric for marketing? It really isn’t, but that doesn’t mean marketers can ignore ROI. Marketers should use ROI in their metrics portfolio when it helps them measure real progress toward objectives or when it serves as a tool that helps manage the journey. Anything that marketing does to generate revenue, such as demand generation and lead generation campaigns, should probably have the ROI metric attached to it. If marketing is able to track the revenue they help create, then the ROI calculation is just a step away.

Note

Marketers should use ROI in their metrics portfolio when it helps them measure real progress toward objectives or when it serves as a tool that helps manage the journey.

Marketing will also find that reporting ROI is useful when it’s necessary to position the value that marketing creates for the organization. Reporting ROI provides proof that marketing is not an expense but a revenue center. The need to report ROI can also arise as a result of internal, political pressures, such as when marketing’s budget or existence is threatened, or simply because it is the best way to justify marketing spending. The case for measuring the ROI of marketing will vary from one organization to another, so each one must determine what makes sense and not assume that it’s always the right or wrong thing to do.

## ROI Measurement Challenges

> We tend to overvalue the things we can measure and undervalue the things we cannot. —John Hayes, CMO, American Express

Even when there is complete agreement within the organization about the need for understanding the ROI of marketing, it isn’t easy to do. Teradata, a provider of big data analytics solutions and integrated marketing applications reports that 75 percent of marketers who attempt to calculate ROI report some kind of problem or challenge in doing so.[⁶](#A978-1-4842-0259-3_6_Chapter.html#Fn6) Even when the will to report marketing’s ROI is present, significant challenges stand in the way.

One of the challenges of measuring the ROI of marketing is determining the scope or level at which to capture data and report ROI. Should marketing report the ROI of the entire marketing function, providing a single metric that represents the macro-view? Or should marketing take a micro-view of ROI and report it for certain aspects of its work, such as demand generation? What about developing an ROI-based business case for some of the investments marketing wishes to make, such as acquiring important technology or executing a campaign? Determining the scope of marketing ROI measurement efforts is one of the first challenges the marketer will face.

Why not report the ROI for the entire marketing function? Why not just determine the return percentage for everything that marketing does? Conceptually, it’s a brilliant idea, but practically, it is difficult. The grander the scale on which metrics are reported can sometimes make those metrics less meaningful. A single ROI measurement is likely to underrepresent marketing’s true value in some areas and overrepresent it in others. The caution here is much the same as it is for using averages. As an old statistician’s saying goes, if your feet are freezing and your head is in an oven, on average, you’re comfortable. Broad or high-level measures, therefore, aren’t always that helpful and at times are unintentionally deceptive.

Understanding the impact on revenue for each thing that marketing does—and capturing the data for these things—is still an impossible task even in today’s digital marketing era. For those things marketing does that have a direct relationship to revenue, such as a digital campaign, measurement is pretty easy. Marketers can link specific revenue transactions to the campaign, enabling precise calculation of ROI. If all that marketing did was execute easily measured campaigns, then determining the ROI for the marketing function as a whole is pretty straightforward.

Obviously most marketing functions don’t just run digital campaigns. They provide a broad scope of services to their organizations, many of which defy revenue impact measurement. For example, how does marketing calculate the revenue impact of promotional items given away at events? How does the keynote address an executive gave at a conference influence revenue? What about the impact of advertising and other brand awareness activities? Everyone can agree that these things have influence and value. But it is difficult, for example, to connect the awareness created by the local sports team sponsorship to the subsequent actions of interested fans who later click on a banner ad because they recognized the sponsor’s brand. These challenges of attribution, which [Chapter 10](../Text/A978-1-4842-0259-3_10_Chapter.html) explores more fully, make precise calculation of marketing’s ROI difficult.

If marketing takes only the measurable aspects of its work and determines their ROI, and then somehow extrapolates that ROI across the entire marketing function, the resulting ROI percentage is almost surely too low, accounting for some of marketing’s revenue-generating activity but not all of it. This approach ends up diluting the ROI calculation for all of marketing by including all marketing costs in the calculation but only the directly attributable revenue. It does not present a true ROI picture.

Some might argue that the flaws with the ROI calculation approach just described don’t matter if the resulting ROI meets minimum requirements. Perhaps they are right much of the time. But this approach to calculating ROI is still an estimate and therefore is at risk of acceptance internally. If the stakeholders inside the company know that the marketing ROI estimate is just that—an estimate—marketing may not see that ROI estimate enjoy full internal support. Even if it is a conservative estimate, marketing should expect some resistance to attempts to use ROI derived this way to justify marketing investments.

It would be ideal if it were possible to calculate the ROI of the whole, ­complete marketing function. The reality for most marketing organizations is that the data just isn’t there or the cost to obtain the data to enable this calculation with some degree of precision is simply too high or too difficult. For this reason, knowing marketing’s ROI at this level has to come with a disclaimer. Marketing must continually educate the rest of the organization on how it creates value and why all of that value isn’t measurable. It’s not wrong to try to report the ROI of the entire marketing function, but that ROI information needs to come with an explanation.

Note

Marketing must continually educate the entire organization on how it creates value and why the ROI metric doesn’t measure all of that value.

What’s much easier is to look at some of the individual components of ­marketing. With the proper data tracking, it’s not difficult to determine the ROI of a pay-per-click campaign, or exhibiting at a trade show, or just about anything marketing does to generate, qualify or nurture leads. There’s value in knowing the ROI from these activities, particularly for comparing one ­campaign to a like campaign. But even this approach has its flaws, and the main one is this: measuring the ROI of a single marketing channel ignores the true, ­multichannel nature of modern marketing. Marketers use a plethora of ­channels to do their work. Ascribing all the credit for a conversion as a ­customer goes through the buying cycle to just one channel completely ignores all the other channels that influenced that conversion. So calculating the ROI for individual marketing components or channels isn’t ideal either.

Other challenges exist for the determination of marketing ROI beyond just the scope and granularity of doing so, such as the data challenge. This ­challenge is multifaceted, representing the skills, alignment, organizational issues, and even cultural issues surrounding the access to or integration of data needed to determine ROI. In fact, the Teradata Data-Driven Marketing Survey reveals that marketers’ number one barrier to ROI calculation is lack of data integration.[⁷](#A978-1-4842-0259-3_6_Chapter.html#Fn7)

Part of marketing’s challenge with data is ownership: many marketing ­organizations don’t control their data destiny, because they don’t have primary responsibility for their own data. In fact, less than one-third of marketers claim to own or control customer data, and over half indicate that they rely on the IT department to gain access to their data.[⁸](#A978-1-4842-0259-3_6_Chapter.html#Fn8) If marketing doesn’t have unfettered access to its data in real time, it will hinder any kind of analytics process, let alone the calculation of ROI.

The data access challenge is frequently made worse by an old nemesis: ­organizational silos. When these exist, the interdepartmental cooperation needed to share data and information for any reason, not just ROI determination, becomes an unproductive diplomatic exercise. Suspicion abounds and turf battles are waged over data, information, and who controls it. Attempting to gain access to the data marketing needs for ROI calculation may require the expenditure of more political capital than marketing has or is willing to spend.

Another factor that impedes marketing’s access to needed data is organizational misalignment. When marketing’s goals are not well aligned with sales goals, or other key organizational goals, chances are quite high that problems exist in a number of other areas, not just in the determination of ROI. Specific to ROI, however, when two closely related functions such as sales and marketing have different goals, they will collect different data. The implication of this alignment problem is that the data marketing needs to do a proper, accurate ROI calculation may not exist.

In some cases, marketing doesn’t have access to the data it needs because it has abdicated responsibility, washing its hands (so to speak) of the ­matter because it requires technical skills not found within the department. The ­trajectory of marketing’s work is unmistakably toward the technical, as Figure [2-1](../Text/A978-1-4842-0259-3_2_Chapter.html#Fig1) depicted. Marketers can no longer maintain technical ignorance, simply outsourcing any technical functions to IT or other groups. Even if marketing isn’t going to manage an analytics process or calculate its ROI, it still needs technical skills to do its work. The marketing department that doesn’t have the necessary technical skills is surrendering control of a growing part of its destiny to someone outside the department that may or may not have ­sympathy for marketing.

The complexity of the sales process, as well as its length, can challenge marketing in its efforts to know its ROI. It’s quite common in the B-to-B environment to see long, complex sales cycles. When this is the case, what’s typically known with certainty is when the process began—at the point a qualified lead was collected—and when the process ended—when a sale was made. In between these two end points there are many influences through many channels that help move a lead toward an eventual sale. Some are easy to track, and others are simply unknown. Longer sales cycles generally involve more of these influences. These influences are things like brand impressions and nurturing activities, and it’s all too easy to lose track of which ones have occurred in any given customer sales cycle or which ones were effective.

The challenges of determining the ROI of marketing are many. What is the CMO to do in the face of pressure to report it? The answer depends on where an organization already is with respect to marketing analytics and ROI. Any marketing organization that desires to report its ROI should focus on the precision of that metric, because with precision comes credibility. The use of ROI as a metric or key performance indicator for marketing is a lot like running a marathon: if you haven’t done it before, you can’t just lace up your shoes and run 26.2 miles your first time out. Instead, start by going shorter distances and slowly build your way up. To keep from getting overwhelmed when starting out with marketing ROI determination, follow an evolutionary path.

*   Getting started: don’t try to “boil the ocean” when it comes to measuring ROI. Get your feet wet by selecting some aspects of marketing’s work for which to determine the return. For example, determining the ROI of paid search or other digital campaigns is within the ability of even those marketing organizations just getting started, because the data is readily available. Here’s the catch: many of the “starter” ROI measurement areas within marketing may have little or nothing to do with the organization’s goals. In the beginning, this misalignment is okay for the sake of learning how to measure and use ROI.  Just make sure that the measurement efforts don’t stay here. In this early learning stage, the ROI measurement results should probably stay within the marketing department.
*   Expand: apply the ROI process and metric to a broader array of marketing’s work. Once some proficiency is gained at measuring and using ROI, it’s important to apply it to things that matter. Align the ROI measurements with goals—both marketing’s and the organization’s goals. As this chapter has discussed, data availability or accessibility is often a challenge in doing this. In this expansion stage, you will also test the cultural waters by beginning to expose marketing’s ROI results to those outside the department. This is still a learning process for marketing, but it also becomes a teaching experience. Marketing needs to help the rest of the organization understand how to use and interpret the ROI data marketing provides. In this expansion stage, marketing shouldn’t be too ambitious by trying to express the ROI of everything it’s doing. The goal here is to gain acceptance for ­marketing’s use of ROI in some strategically selected areas of its work.
*   Integrate: Use ROI as an integral part of the marketing analytics process, applying it as broadly as common sense dictates. The use of ROI as a metric has become integral when it serves as a key influence in decision making. As the organization reaches this stage of maturity, all the caveats to using ROI discussed in this chapter should apply. Use ROI in the proper context, which is as a metric or tool for improvement, not a referendum on marketing. Ensure that there is complete integrity surrounding ROI derivation: don’t use speculative data or estimates in calculations. Make sure the cultural environment is accepting, as ROI can become the sole judge and jury (and sometimes executioner) of marketing’s work, because at this stage, there is full transparency of marketing’s ROI results.

## ROI Is Not Enough

> Most people use statistics the way a drunkard uses a lamp post, more for support than illumination. —Mark Twain

As this chapter discusses, there are pros and cons to using ROI as a ­marketing metric. One extreme in the argument is that ROI is the ultimate marketing metric. The other extreme is that ROI isn’t important and in fact is even harmful as a marketing metric. Neither of these positions represent the place where marketing should live with respect to the ROI metric. As is true for many polarizing arguments, the truth is somewhere in the middle.

Set aside for the moment all the challenges of accurately determining the ROI of anything or everything that marketing does. The ROI metric tells ­neither marketing nor the rest of the organization all it needs to know about ­marketing’s performance or how to improve it. In fact, there isn’t a single metric that serves this purpose. Marketing analytics done properly requires multiple metrics that provide a complete and accurate view. It’s unreasonable to view ROI as that one metric. The financial pragmatists who want to see marketing live and die based on its ROI must realize that other metrics reveal more about true marketing outcomes. Some of these metrics include market share, customer lifetime value, retention rate, and cost of customer acquisition.

Those that feel that ROI has no place in the catalog of metrics must reconcile themselves to the fact that ROI can provide real value. For some marketing activities, ROI is a great performance indicator. True, there are important intangible contributions of marketing that are difficult or too expensive to measure—an ROI metric is clearly not appropriate for these things. But most campaigns and lead generation efforts easily lend themselves to ROI determination and scrutiny. Where it makes sense, marketing should embrace ROI. Furthermore, the marketing idealists must recognize that sometimes it’s simply expedient or politically wise to play the ROI game. If reporting ROI is the path to securing resources or the future of marketing, then marketers should not hesitate to do it.

The right position to take with respect to ROI and marketing is this: use ROI where it makes sense. It is an important metric, but it is a means to an end, not the end itself. Marketers should own the process of determining and reporting ROI, not let that process own them. Recognize that it will take time to gain maturity in using ROI as a marketing metric, because of the challenges of doing so that this chapter discusses. It’s not hard to foresee, however, a day in the not-so-distant future where improved systems for tracking, collecting, and reporting on the needed ROI input data that now is elusive becomes standard and easy to obtain. As marketing technology continues to mature, the ability to precisely know the ROI of the entire marketing function will come within easier reach.

Footnotes [1](#A978-1-4842-0259-3_6_Chapter.html#Fn1_source)

“State of Inbound 2014,”, Hubspot, p. 31, [`http://www.stateofinbound.com`](http://www.stateofinbound.com/) .

[2](#A978-1-4842-0259-3_6_Chapter.html#Fn2_source)

[`http://www.quora.com/What-is-considered-a-good-ROI-for-a-digital-marketing-campaign-for-a-consultancy-company`](http://www.quora.com/What-is-considered-a-good-ROI-for-a-digital-marketing-campaign-for-a-consultancy-company) .

[3](#A978-1-4842-0259-3_6_Chapter.html#Fn3_source)

Phil Johnson, “What’s the ROI of Putting Your Pants on in the Morning? The Dilemma of Measurement and Accountability,” Advertising Age, October 20, 2009.

[4](#A978-1-4842-0259-3_6_Chapter.html#Fn4_source)

James Archer, “Metric-lust: How Well Can You Really Measure Marketing ROI?,” [http://​forty.​co/​metric-lust-how-well-can-you-really-measure-marketing-roi](http://forty.co/metric-lust-how-well-can-you-really-measure-marketing-roi).

[5](#A978-1-4842-0259-3_6_Chapter.html#Fn5_source)

Gordon Wyner, “Marketing Effectiveness: It’s More Than Just ROI,” Millward Brown blog, February 2008.

[6](#A978-1-4842-0259-3_6_Chapter.html#Fn6_source)

“Teradata Data-Driven Marketing Survey, Global,” 2013, Teradata, p. 28, [`http://teradata.com`](http://teradata.com/) .

[7](#A978-1-4842-0259-3_6_Chapter.html#Fn7_source)

“2013 Teradata Data-Driven Marketing Survey, Global,” p. 8.

[8](#A978-1-4842-0259-3_6_Chapter.html#Fn8_source)

“2013 Teradata Data-Driven Marketing Survey, Global,” p. 13.

# 7. Marketing Dashboards

The Voice of the Analytics Process

Think about the experience of attending a professional sporting event where the scoreboard isn’t working or doesn’t exist. Unless you’re very attentive to everything happening on the field, it’s hard to know who’s winning. Or, as the opening paragraph of [Chapter 1](../Text/A978-1-4842-0259-3_1_Chapter.html) describes, imagine driving a vehicle without the benefit of any information the dashboard provides. It’s possible to do so, but it leaves most drivers uneasy and full of questions. How much fuel is left? Am I going too fast or too slow? The dashboard not only provides vital performance information, it also provides reassurance or causes alarm—both important emotional states to keeping the vehicle running well in the long term.

A scoreboard and a dashboard, while different, have similar purposes: to provide a practical progress indicator and accountability mechanism for the marketing strategy as measured by the marketing analytics process. Analytics purists will recognize a difference between a scoreboard (or scorecard as they are often called) and a dashboard. Scorecards are frequently associated with the Harvard Business School business strategy of the balanced scorecard approach.[¹](#A978-1-4842-0259-3_7_Chapter.html#Fn1) They are created via a top-down method that results in identifying Key Performance Indicators (KPIs) that track an organization’s success executing the business strategy. The process is effective when the KPIs of a scoreboard trickle down into the lower levels of the organization, where they are interpreted by departments and individuals in ways that influence their work. While the scoreboard or scorecard is an interface to performance information deemed strategic or critical, it isn’t necessarily a visual one.

Dashboards, on the other hand, provide a visual interface for performance data for any area or business application, not just marketing. They are ­frequently linked to real-time information sources and display performance data in ­summary fashion in the form or graphs or “gauges” that emulate an automobile dashboard. The intent is to share the dashboard, for it to have ­visibility within the marketing department and outside to other departments and ­executives, to facilitate accountability and decision making. Although there are differences between scoreboards and dashboards, the distinctions matter little when it comes to marketing analytics. What does matter is having a easily accessible, visual interface for marketing’s key performance data. For simplicity, this ­chapter will use the term dashboard to refer to this interface.

In the marketing realm, a marketing dashboard is a performance management tool and interface that provides an accessible, visual summary of ­marketing’s KPIs. Stephen Few, founder and principal of Perceptual Edge, offers this ­definition: “A dashboard is a visual display of the most important information needed to achieve one or more objectives, consolidated and arranged on a single screen so the information can be monitored at a glance.”[²](#A978-1-4842-0259-3_7_Chapter.html#Fn2) These dashboards exist in a number of formats, from something as simple as a series of charts printed on paper and posted to a bulletin board to a digital display updated in real time.

Marketing dashboards are important because they render the large ­volume of data a marketing analytics process can produce into a meaningful, ­understandable and actionable summary. As this chapter will discuss, marketing dashboards (intentionally plural, as there are often more than one) evolve in terms of what they report, but at the same time they provide a consistent reference to users in and outside of the marketing organization. Figure [7-1](#A978-1-4842-0259-3_7_Chapter.html#Fig1) displays a screenshot of a sample marketing dashboard provided by the Lenskold Group.[³](#A978-1-4842-0259-3_7_Chapter.html#Fn3)

![A978-1-4842-0259-3_7_Fig1_HTML.jpg](Images/A978-1-4842-0259-3_7_Fig1_HTML.jpg)

Figure 7-1.

This marketing dashboard example provided by the Lenskold Group features gauges for goal tracking, tables with select data details, and trend tracking graphs for key metrics

Within the marketing organization, a dashboard’s primary use is as a performance management tool. The very act of creating and managing a marketing dashboard requires a deep understanding of the source data. Creating dashboards that let marketers see the trends and directions of the big picture view provides otherwise difficult-to-access insights about marketing processes. The marketing team, therefore, uses dashboards to manage and improve its core processes. Sometimes, a review of the latest dashboard provides reassurance that all is well. Other times, the dashboard reveals a trend that, if addressed early, requires minor corrective actions now or major ones later if left unaddressed. Properly constructed and maintained dashboards help marketers spot problems in a timely way and then do something about them.

Note

Marketing dashboards, when properly constructed, help marketers identify problems early and do something to correct them.

Outside of the marketing organization, a dashboard’s primary use is as a communications tool for informing stakeholders. A marketing dashboard is ideally a visually rich, easy-to-understand summary of marketing KPIs. It provides those outside of marketing with a window to see what marketing is doing and what kind of results it is achieving, which in turn provides accountability. In this capacity, marketing dashboards reassure the organization that marketing is performing and to what degree. For most in the organization outside of marketing, these dashboards are their primary interface to marketing.

The development and deployment of marketing dashboards should occur later in the marketing analytics process, after the planning, decisions, and implementation described in [Chapters 5](../Text/A978-1-4842-0259-3_5_Chapter.html) and [6](../Text/A978-1-4842-0259-3_6_Chapter.html) occur. Only after the determination of metrics to support the marketing analytics process is complete can you begin planning what kind of dashboard will best inform stakeholders and constituents about the marketing’s performance.

## Why You Need a Dashboard

> The greatest value of a picture is when it forces us to notice what we never expected to see. —John Wilder Tukey, Exploratory Data Analysis

A dashboard, or more probably a series of dashboards, are critical enablers of the marketing analytics process. In the same way an elevator pitch summarizes the key value proposition of a company or product to prospective customers, a dashboard distills the most important results of the marketing analytics process into an easily digested visual summary. If the output of the marketing analytics process tells a War and Peace–length story, a dashboard provides the Cliff’s Notes version. The marketing analytics dashboard is the executive summary of marketing’s performance report. It is the one thing you can count on that people will view.

Those who are implementing a marketing analytics process for the first time will discover just how much data the process is capable of producing. Those who are managing a mature marketing analytics process are already swimming, perhaps drowning, in an ocean of marketing metrics data. The only way to consistently make sense of it all is through an effective visual summary of that data, otherwise known as a dashboard. Not pairing a marketing analytics process with one or more dashboards is to rob the process of a powerful communications tool.

Note

The failure to use one or more dashboards as part of the marketing analytics process robs it of a powerful communications tool.

In the beginning, when the scope of the marketing analytics process is small, it is possible to use raw data to manage key marketing processes and, to a lesser extent, to inform. But dealing with raw data is tedious, and if the marketing analytics process expands to ultimately encompass all aspects of marketing (as it should), dealing with raw data isn’t a scalable process. It doesn’t take a lot of data for the management of it to become cumbersome and unwieldy without some tools. The use of one or more dashboards is a necessity to help organize and package this data so it can fulfill its intended purpose: create awareness about performance and enable decisions that improve marketing’s effectiveness.

The data from the marketing analytics process mushrooms. It doesn’t take long for a large collection of data to accumulate for just a single metric. As new metrics are added to the marketing analytics portfolio, each with its own data set, the total volume of data can easily grow exponentially. This is a normal occurrence as the analytics process evolves: it doesn’t take long for the volume of data that requires analysis, review, and attention to soar. To meet expectations, the analytics process needs data analysis and reporting tools, and the dashboard is the pinnacle of that reporting.

The process of data analysis, by its very nature, encourages the analyst to delve more deeply into the data. The onion metaphor applies here, with multiple data layers into which the analyst can go deeper and deeper searching for greater meaning. This “zooming in” approach to analyzing marketing analytics data is important and necessary, yielding many insights for those with the discipline to do it. Despite the necessity of taking such a close-up view of the data, the downside is a loss of the big picture. The marketing analytics process must use a microscope to look at the data very closely, but also a telescope to view the data from a distance and see it in its entirety. A dashboard is one of the best ways to provide this latter “big-picture” view. If the analysis of marketing metrics provides a close-up view of the trees, then the dashboard presents a view of the forest.

One reason seeing the big picture is so important is to spot trends that only reveal themselves when analytics data is viewed in aggregate, over time. When a marketer, while analyzing the data, makes a discovery of something that might require action, a good question to ask about the discovery is this: is it an incident or a trend? It’s important to distinguish between incidents and trends in data, as the former may require no action at all, whereas the latter might require significant process changes. The big picture view that a dashboard provides can help the marketing team quickly distinguish between incidents and trends.

Another reason the dashboard is so important is that it presents all the key metrics side by side for easy comparison. Viewing the results of just a single metric could lead to erroneous conclusions. If those results are very favorable or very troubling, attempting to interpret those results outside of the context of other, related metrics can lead to flawed interpretations and bad decisions. The dashboard essentially represents marketing’s vital signs, and a significant part of the value of it is the ability to obtain a complete view of all those vital signs. A particular metric that seems troubling when viewed in isolation is perhaps less alarming when viewed alongside related metrics. Conversely, a metric that seems fine in isolation may create necessary alarm when seen as part of the big picture. This holistic view of marketing analytics data is another reason the marketing analytics process needs one or more dashboards.

The view the rest of the organization often has of sales and marketing is ­summarized by this question: “what have you done for us lately?” This question represents a level of skepticism about performance and contribution that is frequently directed marketing’s way. Marketing’s use of dashboards helps keep this question from being merely rhetorical, and instead provides an accountability interface that clears away the fog around how marketing is helping the organization succeed. This benefit is perhaps the most important reason for marketing to adopt the use of dashboards with its analytics process, even if they are not needed for any other reason.

There are plenty of reasons to use a dashboard as part of the marketing ­analytics process—to see the big picture, spot trends, compare metrics side by side, and show accountability. Any one of these reasons is more than ­sufficient to justify developing dashboards to support the marketing analytics process. Together, these reasons compel the use of marketing dashboards.

## Keys to Success with Dashboards

> If you just focus on the smallest details, you never get the big picture right. —Leroy Hood[⁴](#A978-1-4842-0259-3_7_Chapter.html#Fn4)

The marketing dashboard is a simple concept to grasp, and the term itself is very descriptive, leading most people to a solid understanding of what a marketing dashboard is. The challenges with marketing dashboards lie elsewhere, particularly in their implementation, use, and acceptance. Since dashboards effectively serve as the organization’s interface to marketing’s performance, it’s important to implement them properly. The stakes are high: if the dashboard(s) that marketing publishes become the primary influencer of the opinion of marketing, the marketing team cannot afford to get this wrong. A cavalier approach to the marketing dashboard may cost the marketing team in the areas of reputation, confidence, and political capital—even if they are executing the marketing analytics process well. This section describes the key considerations for implementing marketing dashboards well.

There are many aspects of marketing dashboards the marketer must ­consider, all of them important, but one of the most important considerations is ­alignment. As [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html) discusses, the marketing analytics process and the metrics that support it are a reflection of higher level business goals. In fact, the goals for and metrics of the marketing analytics process are derived from business goals and objectives. A thread of continuity should run from the highest level of business goals to the most detailed marketing metric. In other words, complete alignment should exist, and that alignment must extend to the dashboards that marketing publishes.

This notion of aligning the dashboard to the objectives and the metrics ­associated with them seems intuitive. In actual practice, however, the ­metrics can easily be untethered from the objectives, and so can the dashboard. When this happens, it usually isn’t caused by a deliberate intent to deceive. Rather, it’s a reflection of inexperience or expediency. In the same way that [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html) recommends making sure the analytics process and its underlying metrics align to the business strategy and goals, marketing must ensure that the ­dashboards report data that is also aligned with something that matters, and not just data that looks good on the dashboard.

What goes a long way toward ensuring that marketing’s dashboards are well aligned is the ideal selection of metrics. [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html) discusses the need to select metrics for the marketing analytics process that are based on business objectives. If the recommendations from [Chapter 5](../Text/A978-1-4842-0259-3_5_Chapter.html) are followed, then the input data for marketing’s dashboards should already align. Assuming compliance with those recommendations, the challenge becomes one of determining which metrics to include on the dashboard. What is the right level of detail?

The marketing dashboard isn’t meant to serve as a comprehensive catalog of every metric tracked by the analytics process. Instead, it summarizes key results for various audiences who have different informational requirements. In the early going of the marketing analytics process, the compendium of metrics is usually small, and it is therefore possible to summarize each one on a single dashboard. However, as [Chapter 6](../Text/A978-1-4842-0259-3_6_Chapter.html) shares, the marketing analytics process expands in scope as it matures, including more metrics and collecting more data. It doesn’t take long for the list of things that a dashboard could report to exceed the things it should report.

Note

Marketing dashboards aren’t intended to track every metric in the marketing analytics catalog. Instead, they should summarize key results to meet the informational needs of the audience for the dashboard(s).

The best way forward when it comes to the ideal selection of metrics for the marketing dashboard is to understand the audience for the dashboard. What do they need to know? Which metrics are critical to enable the audience to have an accurate understanding at the right level of marketing’s performance? When this approach is taken, it often leads to creating different versions of a marketing dashboard for specific audiences. For example, the “Executive Marketing Dashboard” is in regular use by many organizations, and it provides information and detail at a level desired by members of the C-suite. Marketing should not just publish dashboards but attempt to understand the audiences for its dashboards and provide them with the information that is most helpful to them.

When marketing creates, publishes, and curates dashboards as part of the analytics process, it often has to deal with the issue of accurate data. The success of the entire analytics process hinges on the availability of accurate data. What many marketers learn is that accurate data isn’t always easy to obtain, particularly in the early stages of the process. The collection of marketing analytics data often requires tuning, tweaking, and testing methods across several measurement cycles to get it right and gain confidence in the data.

Marketing dashboards are visual presentations of this data: consider them the “eye candy” of the marketing analytics process. As such, they are often impressive and dazzling in appearance, and it’s easy to create a stunning display of data on a dashboard that impresses but is also entirely inaccurate. However, the very fact that data is presented in a dashboard seems to give that data authority and credibility, even when it doesn’t deserve it.

Marketing needs to guard against letting inaccurate data creep into one of its dashboards. Until an analytics data source is accurate and reliable, it should never appear on a dashboard, because its very presence there implies ­accuracy and trustworthiness. This is a reputational and integrity issue for marketing, and it must always err on the side of presenting no data before its time, despite how good that data may look as a component of a dashboard.

As marketers sort through the metrics and select data for the dashboards they create, it is ideal to provide a real-time view of that data. It’s easy to think of dashboards as summaries of historical data and trends. It is useful to view a year’s worth of data for a key metric at a glance. However, having a real-time view of what is happening right now in the moment is also vital. The automotive dashboard, the metaphor for the marketing dashboard, does just that: provides real-time data for speed, fuel level, and other performance metrics. Marketing dashboards provide a lot of value when they are able to provide this up-to-the second information for certain metrics.

The reality is that marketing dashboards need to supply both historical and real-time information, because dealing in one of these types of information is of limited value. Ultimately, if they don’t provide a broad enough view of information, their perception as an information source suffers, dragging the perception of marketing down as well. However, there are challenges to displaying real-time data in a dashboard, and these challenges range from the availability of the information to capturing that data in a dashboard. The need or desire to provide real-time data in a dashboard is often foiled by these technical challenges surrounding the data.

One of the considerations that can facilitate reporting real-time data on a marketing dashboard is direct integration with data sources. Since there really is no “system of record” for marketing, the data that feeds the marketing analytics process can come from a number of different and even unrelated systems. To the extent that it is possible, marketing should link these data sources directly to the marketing dashboards through the use of any available integration paths, such as application programming interfaces (APIs). Figure [7-2](#A978-1-4842-0259-3_7_Chapter.html#Fig2) shares research data that shows various data sources and the frequency with which they are part of a marketing analytics process.[⁵](#A978-1-4842-0259-3_7_Chapter.html#Fn5)

![A978-1-4842-0259-3_7_Fig2_HTML.jpg](Images/A978-1-4842-0259-3_7_Fig2_HTML.jpg)

Figure 7-2.

Marketing analytics data is sourced from a variety of systems, many of which can provide data to a dashboard via an API

When these integration paths exist, marketers should definitely use them to eliminate the tedious manual loading or copying of data into dashboards. Cutting and pasting or manually importing data will get the job done, but the process can quickly become unwieldy, and when it does, it becomes a barrier to producing quality dashboards. Even though some technical know-how is required to exploit these integrations, they are worth putting in place from the start so that an entire dashboard initiative doesn’t collapse under the weight of a manual data populating process.

Fortunately, the technical heavy lifting associated with integrating all the data sources necessary for curating a marketing analytics dashboard is ­becoming easier. The Marketing Technology Landscape Supergraphic[⁶](#A978-1-4842-0259-3_7_Chapter.html#Fn6) referenced in [Chapter 2](../Text/A978-1-4842-0259-3_2_Chapter.html) identifies at least 35 vendor solutions for creating dashboards and other data visualizations. One thing most of these solutions have in ­common is a set of data connectors and APIs that make integrating data from varied sources into a dashboard much easier, and in many cases, automatic. Though it’s certainly possible to create a dashboard using a spreadsheet or even printed charts and graphs, getting data into them is a manual process, and this often becomes the failure point of publishing dashboards.

This author recently visited a client who published a paper-based dashboard by printing charts and graphs and posting them to a conference room wall. The array and variety of information on display was impressive, as was its organization on two walls of the conference room. On close inspection, most of the graphs were several months out of date, illustrating the problem that results when dashboards don’t integrate to and automatically populate with the data on which they are based. The use of a dashboard solution that ­eliminates this problem is highly recommended and in fact is a key enabler of success for a dashboard.

The most important considerations for implementing marketing ­dashboards are sharing and collaboration. No matter how elaborate or how well constructed a dashboard is, it is just a piece of art if it doesn’t motivate the people who see it to take action. For the decision making to occur, sharing must first happen. What happens too often is that marketing thoughtfully ­creates and publishes one or more dashboards, but they never “escape” the marketing department and get seen by those who need to do something on the basis of the data they contain. The process of publishing a dashboard needs to include consideration of how to share the dashboard with those that need to see it. Each of the considerations discussed so far contribute to sharing of and collaboration around marketing dashboards.

Note

A marketing dashboard that doesn’t motivate the people who see it to take action is just a piece of art. Action comes from sharing and collaboration of dashboards.

It is the effective sharing of dashboards that enables collaboration, and this collaboration is what creates the value from the dashboarding effort. A dashboard is a communication and collaboration tool, and if the collaboration doesn’t occur, producing dashboards essentially amounts to busy work. Collaboration should occur across several levels. Intradepartmental collaboration among members of the marketing team is the first level, and is the ­easiest to facilitate because often these team members share physical ­proximity. Still, a CMO should never assume that collaboration is occurring as a natural by-product of the existence of a dashboard. CMOs need to model the collaboration and create the expectation of how it should take place and what should result from it. As Figure [7-3](#A978-1-4842-0259-3_7_Chapter.html#Fig3) illustrates, collaboration around marketing analytics data and the dashboards that display it isn’t always a certainty.[⁷](#A978-1-4842-0259-3_7_Chapter.html#Fn7)

![A978-1-4842-0259-3_7_Fig3_HTML.jpg](Images/A978-1-4842-0259-3_7_Fig3_HTML.jpg)

Figure 7-3.

More than a third of organizations have minimal to no collaboration occurring around their marketing analytics data

What does this collaboration look like? The results on a dashboard are a catalyst for action, so when studying the data a dashboard displays, reviewers should concern themselves with the implications of what they’re seeing. The questions that dashboard results should prompt are simple, but the actions taken profound:

*   What do the results the dashboard displays say about meeting the underlying objectives?
*   What needs to happen or change because of the results the dashboard displays?

Use the first question to determine if the metrics summarized on the dashboard indicate progress toward or regression from goals. This initial determination essentially amounts to marketing getting its bearings and knowing where it is with respect to achieving goals. Before any actions are taken or course correction attempts are made, marketing simply needs to know where it is. If the dashboard indicates progress, often nothing needs to occur, as the dashboard indicates current marketing processes are delivering hoped-for results. Still, it’s as valuable to understand why marketing is achieving favorable results as it is to determine why it isn’t. Collaboration around this first question is usually quite simple: a review of the dashboard results by all interested parties to determine if there is agreement around what they mean.

Dashboard results that indicate progress is not being made toward objectives should trigger problem determination efforts. This is the point at which collaboration is most valuable, first to understand the root cause of the results and then determine what to do to change them. Marketers need to understand that the existence of the dashboard doesn’t ensure that this collaboration will occur. Marketing must lead the collaborative process, and the dashboard becomes a tool that facilitates that collaboration.

The tools marketing chooses for its analytics process and dashboard reporting do affect how well collaboration occurs. Certain analytics tool choices facilitate collaboration better than others, and research[⁸](#A978-1-4842-0259-3_7_Chapter.html#Fn8) reveals that some tool choices actually predict better collaboration:

Data visualization tools Statistical analysis software Business intelligence tools Presentation software or tools

Although the tool choices listed above help drive collaboration, this same research identified one tool choice whose use actually lowered collaboration: spreadsheets. This is a concern because spreadsheets are also one of the most frequently used marketing analytics tools. They have the advantage of ubiquity, and most marketers need no training on using spreadsheets. It’s relatively easy to build a spreadsheet using analytics data, crunch the numbers, and produce some dashboard graphs with a few clicks. The problems occur when it comes to sharing spreadsheet data and tracking various versions of them and then managing distribution. Spreadsheets weren’t designed as a collaboration tool. Marketers will likely always use spreadsheets as part of the marketing analytics process, but they should understand their limitations and seek to use tools that better promote collaboration and decision making around their analytics data.

## Achieving Dashboard Nirvana

> The journey of a thousand miles begins with a single step. —Lao Tzu

An earlier section of this chapter alluded to the fact that not just one but a series of dashboards are ideal companions for the marketing analytics process. As the marketing analytics process matures, it is highly probable that it will use a series of dashboards to present data across some very specific areas of focus. How many dashboards should be used? The answer is not the same for every marketing organization, but there is a simple model that works well: an executive-level dashboard that summarizes the most important metrics, and then supporting dashboards that provide detail in strategic areas. The goal is to make the information accessible and communicate it effectively, not to make dashboards dense with data. Build and organize marketing dashboards with this goal in mind.

Creating the right collection of dashboards is an evolutionary process. Marketers shouldn’t endeavor to put every dashboard it may ultimately need in place right out of the starting gate. Begin with the executive-level dashboard that provides just the broadest view of metrics tracked as part of the analytics process. Marketing should customize this executive-level dashboard to match the informational needs of the audience for it, and as the name implies, it is usually the C-suite. Here is a sample configuration of data elements for the executive-level marketing dashboard:

*   Marketing program metrics: overall performance ­indicators such as revenue generated and current budget status.
*   Customer program metrics: customer acquisition costs, customer retention rate, customer lifetime value, Net Promoter Score, and so on.
*   Lead generation metrics: new leads by channel, cost per lead, conversion rates, opportunities created, and so on.
*   Website metrics: traffic sources, top pages, unique visitors, bounce rate, time on site, and so on.

Perfecting the executive-level marketing dashboard is an iterative process. It will require some tinkering to structure the right mix of metrics to clearly and concisely communicate the state of marketing’s performance. Once a satisfactory configuration is achieved, expect future changes to accommodate a changing marketing landscape and strategy. It is best to understand that the executive-level marketing dashboard is fluid with respect to the results it needs to communicate.

With an executive-level marketing dashboard in place, a series of supporting dashboards are probably in order. The marketing team should create additional dashboards that reflect their priorities and the need to convey information about them. There is no prescribed number of dashboards to have, and more dashboards are not necessarily better than fewer dashboards. As previously stated, dashboards are management and communications tools, so have enough to do these jobs well. There are, however, specific dashboards that are commonly found in marketing organizations. Here is a partial list of themes for other, more specific marketing dashboards:

*   Demand generation metrics: a dashboard for reporting on the performance of campaigns and metrics associated with the flow of leads from capture to qualification and conversion.
*   Content marketing metrics: a dashboard for tracking the content creation, publication, and consumption process. The content marketing dashboard should include metrics for volume as well as engagement, such as sharing.
*   Social media metrics: a dashboard for tracking the activity, engagement, and reach of social media marketing efforts across all social media networks in use.
*   Public relations metrics: a dashboard for tracking media mentions by frequency, source, topic, type, tone, impressions, and impact (response).
*   Events marketing metrics: a dashboard for tracking events by type, cost, participation, leads, and revenue generated.
*   Digital marketing metrics: a dashboard for tracking the results of digital marketing efforts such as impressions, landing page views, conversion rates, leads captured, and revenue generated.
*   Web marketing metrics: a dashboard for tracking website effectiveness through traffic sources, page views, unique visits, referrals, keywords, search rank, and other key metrics.

Just from scanning this partial list of marketing dashboards, it’s easy to see some redundancies in the data they track. It’s okay, and in fact necessary, for these dashboards to report data that also exists on other dashboards. Each dashboard should present a complete summary of the marketing initiative it was designed to track, and this will result in the same data appearing on multiple dashboards. The need to do this illustrates the challenge of manually populating dashboards with data: you have to keep track of all the places data needs to appear and make sure the data is consistent. The more dashboards marketing publishes, the more likely it will encounter this version control challenge of manually populating dashboards with data.

The marketing dashboard is an important component of the marketing ­analytics process. It is the way to give the process a voice and facilitate sharing and collaboration around the marketing results, which will help marketing get better at what it does. Once marketing has determined which metrics it needs to track and has begun to collect reliable data for the analytics process, it is time to start publishing that data in the form of a dashboard.

Footnotes [1](#A978-1-4842-0259-3_7_Chapter.html#Fn1_source)

“Scorecards and Dashboards,” Klipfolio, [`http://www.klipfolio.com/resources/articles/what-are-dashboards-scorecards`](http://www.klipfolio.com/resources/articles/what-are-dashboards-scorecards) .

[2](#A978-1-4842-0259-3_7_Chapter.html#Fn2_source)

Stephen Few, “Common Pitfalls in Dashboard Design,” February 2006, p. 2, [`http://www.perceptualedge.com/articles/Whitepapers/Common_Pitfalls.pdf`](http://www.perceptualedge.com/articles/Whitepapers/Common_Pitfalls.pdf) .

[3](#A978-1-4842-0259-3_7_Chapter.html#Fn3_source)

See [`http://www.lenskold.com/solutions/dashboards.html`](http://www.lenskold.com/solutions/dashboards.html) .

[4](#A978-1-4842-0259-3_7_Chapter.html#Fn4_source)

“Seattle’s Leroy Hood receives National Medal of Science at White House,” Valerie Bauman, Puget Sound Business Journal, February 1, 2013; [`http://www.bizjournals.com/seattle/blog/2013/02/seattles-lee-hood-receives-national.html?page=all`](http://www.bizjournals.com/seattle/blog/2013/02/seattles-lee-hood-receives-national.html?page=all) .

[5](#A978-1-4842-0259-3_7_Chapter.html#Fn5_source)

“Sales & Marketing Analytics: Gauging and Optimizing Performance, Demand Metric, December 2013, p. 13; [`http://www.demandmetric.com/content/sales-marketing-analytics-benchmark-report`](http://www.demandmetric.com/content/sales-marketing-analytics-benchmark-report) .

[6](#A978-1-4842-0259-3_7_Chapter.html#Fn6_source)

Chief Marketing Technologist Blog, Scott Brinker, January 12, 2015; [`http://chiefmartec.com/2015/01/marketing-technology-landscape-supergraphic-2015/`](http://chiefmartec.com/2015/01/marketing-technology-landscape-supergraphic-2015/) .

[7](#A978-1-4842-0259-3_7_Chapter.html#Fn7_source)

“Sales & Marketing Analytics,” p. 25.

[8](#A978-1-4842-0259-3_7_Chapter.html#Fn8_source)

“Sales & Marketing Analytics,” p. 27.

# 8. Tools and Technologies

Marketing Analytics Enablers

From the beginning of the species, when humans had work to do, we usually invented some sort of tool to help us do it. Any time we recognize the need to complete a repetitive task, we almost immediately begin looking for ways to simplify that task by inventing tools, and we tend to refine those tools through use. Tools allow us to complete work faster, achieve greater precision and efficiency, or simplify work so it requires less skill or effort.

Marketing analytics is certainly a discipline that benefits from the presence of tools; beyond that, tools enable the process. Without some basic tools and technology, marketers will find it very difficult to consider anything beyond the simplest metrics in their effort to leverage analytics to improve their outcomes. With tools, marketers can build and manage a sustainable analytics process, one that is sufficiently broad in scope and capable of considering the large volumes of data necessary to provide a true picture of marketing performance. Having the right collection of tools and technology is therefore essential to creating and managing an effective marketing analytics process.

Note

Marketing analytics is a technology-enabled process. Having the right technology in place and tools available is essential to success.

In the context of marketing analytics, there are both “tools” and “technology,” and although these terms are often used interchangeably, it is useful to create a distinction to separate the must-have technology from the tools that provide more specialized functionality:

*   Technology: the base platforms, systems, or clouds of technology that enable core marketing processes and results tracking.
*   Tools: focused software solutions that enable specific functions, such as the dashboard functionality discussed in Chapter [7](../Text/A978-1-4842-0259-3_7_Chapter.html).

This division between technology and tools is not sacred; it exists here simply to describe broad differences between these categories.

As the marketing technology landscape continues to expand, with new solutions emerging on a continuous basis, and coalesce, with vendors acquiring each other, the distinction between technology and tools is blurring. Many functions that once were available only in separate, standalone tools are now integrated into the base systems that marketers use. The purpose of drawing the distinction is to clearly identify the technology that is essential infrastructure for marketing, not just analytics, from the nice-to-have tools that simplify specific analytics functions. The balance of this chapter examines the technology and the tools in greater detail, providing examples of how marketers are using them.

## Marketing Analytics Technology

> The number one benefit of information technology is that it empowers people to do what they want to do. It lets people be creative. It lets people be productive. It lets people learn things they didn’t think they could learn before, and so in a sense it is all about potential. [¹](#A978-1-4842-0259-3_8_Chapter.html#Fn1) —Steve Ballmer

As Chapter [5](../Text/A978-1-4842-0259-3_5_Chapter.html) discussed, there is no “system of record” for marketing. The marketing organization uses an expanding mix of technologies and platforms to fulfill its mission. In this mix are some platforms that are virtually indispensable to the marketing team, and chief among them is marketing automation. Demand Metric defines marketing automation as the strategies, processes, and software technology that enable marketing departments to automate, measure, and improve the performance of strategies, activities, and workflows.[²](#A978-1-4842-0259-3_8_Chapter.html#Fn2)

In the not-too-distant past, marketing automation was considered a luxury and was found primarily in larger organizations. As is often the case when technology evolves, marketing automation has become more capable and affordable, and marketers are better educated about its benefits. As recently as mid-2013, just under 50 percent of marketers reported using marketing automation, and in early 2015, that number has risen to between 61 and 70 percent (depending on the size of the enterprise), with larger enterprises slightly more likely to have marketing automation in place.[³](#A978-1-4842-0259-3_8_Chapter.html#Fn3) This marketing infrastructure has clearly hit the steep section of the technology adoption curve.

Early in its life cycle, marketing automation was viewed as a productivity aid for marketers. It provided a better way to build campaign landing pages and send out email blasts, and it had some nice reporting and metrics features as well. Through sustained use, marketers began to understand the value of all the metrics data captured in an automation system, and they began to understand how to use it to their advantage. Now, the solution has evolved to the point where “marketing automation” as a solution category no longer does justice to its full range of capabilities. The automation dimension is still there, but it really is better thought of as a “marketing intelligence” platform that serves as an integration hub and data aggregation point for almost every other solution in the marketing technology stack.

There are several implications of this high degree of integration that can exist between marketing automation and other solutions or tools. The implication that matters most to this discussion is this: marketing automation is a valuable aggregator and repository of data for the marketing analytics effort. This data, coupled with the tools for analyzing it that are built into marketing automation platforms, make it a cornerstone technology for marketing analytics under one big assumption: the platform gets fully implemented. The organizations that stop short of a complete implementation will not get the marketing analytics benefits available to them. When marketing automation is fully implemented, it provides as complete a view of a customer’s journey as one can obtain.

Note

Marketing automation is a cornerstone technology for marketing analytics because it aggregates and serves as a repository for data the process requires.

To understand the importance of marketing automation in enabling analytics, consider how the buying journey typically progresses. For 81 percent of business-to-business buyers, that journey begins with a web search for a solution.[⁴](#A978-1-4842-0259-3_8_Chapter.html#Fn4) When that search leads to a vendor that uses marketing automation, data about the visit is captured. The subsequent interactions with that prospect are also captured. Should the prospect share his or her email address to gain access to content or register for events, the system then tracks email opens, clicks, shares, and other metrics. The system will track which web pages the prospect views and how much time was spent on each. Should the prospect register for a webinar or interact with the vendor through social media, all of these things are logged.

By capturing information about every digital encounter a prospect has with a vendor, the marketing automation system accumulates a historical record of the prospect’s journey with the vendor. Companies that use marketing automation can make intelligent, profitable use of the detailed metrics the system solutions collect about the buyer’s journey. This data lets marketers understand with precision how long the journey takes, where it bogs down or fails, learning where to deploy content, offers, or other promotions to speed up the process and convert prospects to customers. Reflecting on this capability, one marketer with a commercial real estate technology company shared that they were integrating their marketing automation system with their customer relationship management (CRM) system to “collect more info about our customers and use this data to dictate the cadence of messaging and the content they receive from us throughout the customer life cycle.”

UrbanBound, a relocation management software and solution provider, uses the HubSpot marketing automation platform. Aria Solar, inbound marketing coordinator at UrbanBound, shares these comments about the solution’s importance:

> I’m not sure how anyone effectively does their job in the marketing field without a platform like this. HubSpot has been one of our greatest weapons, especially at a startup, as we’re going up against big companies with a strong and solidified groundwork. The data that HubSpot provides is simply unparalleled—any number you could possibly want to know, whether it’s website visits, page performance, blog hits, social media metrics, CTA clicks (and anything else you can think of), is all found within the portal. The easy to use interface allows you to aggregate all of your marketing efforts in one place, giving you numerical-proven insight into exactly what is and is not working. The depth that the information goes into in the platform is simply invaluable—it drives everything that our team does.

Solar continues, reflecting on the shift toward analytics, metrics, and data-driven marketing:

> Long gone are the days when marketing was viewed as “arts and crafts”—and this is largely due to platforms such as HubSpot. Marketing has shifted to a completely data-driven industry, powered by conversion rates, click-to-open percentages, SEO, keyword analytics, and submission rates. HubSpot not only allows you to see all of these metrics, but it guides you through the whole process with a robust knowledge center. HubSpot has transformed our company into a marketing powerhouse.

Marketing automation solutions have a lot of affinity with CRM solutions, and they are often integrated. When they are, the marketing team has an even broader perspective of the buyer’s journey with the addition of data about sales interactions with prospects and customers. Whether implemented separately or integrated with CRM, marketing automation platforms are the foundation for marketing analytics. Marketers who want to build and sustain a marketing analytics process should prioritize the implementation of a marketing automation solution. Marketing automation is rapidly evolving into the system of record that marketing organizations have lacked.

Note

Marketing automation serves as the foundation for the marketing analytics process, on which marketers can build and sustain an analytics process.

These solutions are only growing in importance and value. Today their analytics capabilities show what happened in the past, and this solution space is on the cusp of integrating predictive marketing analytics capabilities into their solutions. Predictive analytics, covered in greater detail in Chapter [10](../Text/A978-1-4842-0259-3_10_Chapter.html), will provide marketers with very accurate models that forecast future purchase decisions based on prospect behavior. The organizations that have mature, complete marketing automation implementations are poised to be the first to exploit this game-changing capability. Marketing automation is an essential platform for those who want to use analytics to become more revenue-driven and results-oriented.

## Marketing Analytics Tools

> We become what we behold. We shape our tools and then our tools shape us. [⁵](#A978-1-4842-0259-3_8_Chapter.html#Fn5) —John M. Culkin

Having marketing automation installed and fully implemented provides a great foundation for marketing analytics. As a marketing team develops proficiency with the analytics process, it begins to exploit the insights the process yields. When this maturing takes place, it often spawns a desire to expand the reach and range of the marketing analytics, which requires additional tools and data. The selection of specific tools to use is a challenge for any organization that wades into the deeper end of the marketing analytics pool. Of course, the type of metrics chosen for its analytics portfolio will drive tool selection, but there are easily hundreds of potential tools available. Where there was once just a handful to choices, marketers now have an embarrassment of riches in the analytics tools category.

This chapter does not attempt to provide a comprehensive catalog of marketing analytics tools, because the offerings change so quickly and some of the information would be obsolete at the time of publication. Instead, the remainder of this chapter will describe two broad classes of marketing analytics and include examples of tools that exist in these categories. The goals here are to make the reader aware that tools exist for a wide variety of purposes and applications and encourage the reader to further research what these are and the latest offerings on the market.

Because there are so many tools available for marketing analytics, it is easier to separate them into two broad categories based on high-level function, and then consider some example tools within each of those categories:

Strategic: tools and systems for business intelligence, customer intelligence, understanding buying behavior, advanced attribution, and predictive analytics. Operations and logistics: tools and systems for managing, testing, and optimizing a web presence, mobile, multichannel campaign performance, demand, and geo-modeling.

There is some overlap in the names of these two categories, as all analytics are strategic, and therefore by default so are the tools used to support them. However, even within the realm of strategic analytics, there some that are more operationally oriented than others. Most marketing analytics tools fall neatly into these two categories, which the rest of this chapter explores.

### Strategic Marketing Analytics

Strategic marketing analytics are those that help provide direction to the marketing function. The work done under the banner of “strategic” analytics ensures that the marketing team is doing the right things, those that have the greatest impact. These analytics guide marketing to invest its limited resources in the best possible way. To provide the insights necessary to guide the team, strategic marketing analytics uses tools for customer and business intelligence gathering. There is a forward-looking aspect to this type of analytics. Where many operations and logistics analytics are backward-looking—measuring historical results of campaigns and programs—strategic marketing analytics helps peer ahead by providing predictive results.

Most organizations now collect exponentially more data about their business and customers than they once did. As an example of this data explosion, consider how many auto insurance providers now offer apps and devices to help with policy quoting. These devices plug into a vehicle and record a stream of data about how that vehicle is driven. Where there was once a single customer record in the database, there is now substantially more data about a customer’s driving patterns. This example is just one of many that illustrate how much data is available to companies about their customers. The challenge is making good use of that data, which often requires retrieving it from disparate systems and performing some analysis to reveal insights that influence strategy. This is where business intelligence (BI) systems come into play.

BI refers to the software tools that help organizations analyze virtually any source of raw data. The purpose of BI tools is to turn that data into actionable information. They simplify the process of querying data from multiple sources, performing various analyses on it, and then reporting the results, often in the form of a dashboard or other data visualization. BI tools put a lot of power in the hands of the user without requiring technical programming skills to get results. A common challenge with using them is the quality of the source data. Some remarkable and sophisticated analyses are possible with BI tools, but if the data on which the analyses are performed is suspect, the results are unreliable.

Gartner, an information technology research and advisory company, publishes a BI and Analytics Platforms Magic Quadrant,[⁶](#A978-1-4842-0259-3_8_Chapter.html#Fn6) a visual summary analysis of the direction and maturity of markets and their vendors. The most recent version of the Magic Quadrant categorizes several vendors as “leaders,” including IBM, Information Builders, Microsoft, MicroStrategy, Oracle, QlikTech, SAP, SAS, Tableau, and Tibco Software.

Customer intelligence (CI) is conceptually similar to BI but with a different focus. Where BI has traditionally been oriented around financial reporting, the focus of CI is on understanding customer behavior. Using CI tools, marketing organizations can analyze the customer data they have amassed, usually in CRM systems, using the intelligence locked in this data to improve the customer experience and ultimately generate more revenue from customers. CI helps marketers better understand the customer experience and answer questions like how to increase the share of wallet captured, which segments are most important, or how to improve customer retention. The insights gained from using CI tools provide marketers with a much deeper understanding of who the customers are, how they behave, and why.

Many of the CI tool and solution providers are the same as for BI. Gleanster Research, a company that surveys users and publishes the results in benchmark reports, covers the CI solution space. It has compiled a list of vendors that provide a CI solution, including Infer, Lattice Engines, Lithium, and Marketforce.[⁷](#A978-1-4842-0259-3_8_Chapter.html#Fn7)

CI tools are increasingly providing their users predictive analytics capability. Most marketing organizations are charged with generating leads, so they invest in lead generation activities across multiple channels, using metrics to monitor the lead flow and effectiveness of the various channels and using data from the analytics process to optimize the mix, score the leads, and then pass the qualified leads on to sales. This process is a big part of what many marketing organizations do, and their effectiveness in doing so has a direct effect on revenue. This process has a lot of moving parts, so it takes constant care, attention, and analytics monitoring to ensure that the process is performing at acceptable levels.

The lead generation process is heavily dependent on technology and digital marketing approaches, so this process produces a lot of analytics data. Indeed, managing the lead generation process without analytics data would involve guesswork to determine which channels were performing best. Despite the digital nature of this process, very little of the technology involved in lead ­generation has been about enabling this process in a predictive way. Most of the analytics surrounding the lead generation process provide a historical view of process performance. In other words, it’s possible to know with a great ­precision what has happened through metrics that are largely backward-looking. Now CI and predictive analytics solutions are providing users with the ability to accurately predict the conversion of future of leads in the sales funnel.

In the real world, predictive analytics are allowing marketers to create models that lead to customers most likely to buy, thus accelerating revenue. AgilOne, a provider of a cloud-based predictive analytics solution, documents ten use cases for predictive analytics in its ebook, The Definitive Guide to Predictive Analytics for Retail Marketers.[⁸](#A978-1-4842-0259-3_8_Chapter.html#Fn8)

Behavioral clustering: helps better understand how customers behave while purchasing. Product/category-based clustering: segments customers into groups based on which products they purchase. This insight lets marketers make more intelligent choices about which offers to extend to customers. Brand-based clustering: identifies brand affinity groupings that customers have. For example, customers who prefer brand A also prefer brand C, but not brand B. Predictive lifetime value: predicts future lifetime values of customers. Useful for setting spending parameters on costs of new customer acquisition. Propensity to engage: predicts how likely it is for a customer to take certain actions, such as clicking on a link in a promotional email message. Propensity to convert: predicts the likelihood that a customer will respond to call-to-action offers extended to them via email, direct mail, or other means. Propensity to buy: identifies customers who are ready to make a purchase, allowing marketers to trigger those purchases with a special offer, or market more aggressively to those who aren’t ready to buy. Upsell recommendations: helps increase the average size of order by predicting premium products or greater quantities in which a customer might have interest. Cross-sell recommendations: at the time of purchase suggests other products that are frequently purchased together. Next sell recommendations: after a customer has already purchased a product, suggests products that are likely next purchases.

Predictive analytics is a relatively recent phenomenon, but it is gaining rapid acceptance. As is the case with most leading edge technologies, it initially gained a foothold with large enterprises as a specialty solution. It continues to evolve, moving downmarket and becoming more accessible as its functionality is added to marketing automation and CI platforms.

Advanced, data-driven attribution (sometimes referred to as cross-channel attribution) is a method that precisely allocates fractional credit to elements of the full stream of content or media to which a customer is exposed on their buying journey. Chapter [10](../Text/A978-1-4842-0259-3_10_Chapter.html) provides greater detail on advanced attribution, but it merits a mention here since it falls within the realm of strategic marketing analytics.

Marketers that use advanced attribution can know precisely which offers, elements, and media stream components really produced a conversion. This level of insight into which components of marketing’s multichannel campaigns really deserve credit has long been considered impossible or unknowable, but with advanced attribution, it is a reality.

Advanced attribution is important, because it provides marketers with the data to make intelligent decisions about their media mix. Marketers once relied on intuition and guesswork to determine the right blend of email, online display advertising, social media advertising, paid search, and direct mail. Now through advanced attribution, accurate data is available about each of the channels in use and how they perform together. The advanced attribution solution space experienced significant consolidation in 2014.[⁹](#A978-1-4842-0259-3_8_Chapter.html#Fn9) Forrester Research has published an evaluation of cross-channel attribution solutions that covers the following vendors: Abakus, AOL/Convertro, eBay Enterprise, Google, Marketing Evolution, MarketShare, Rakuten DC Storm, and Visual IQ.[^(10)](#A978-1-4842-0259-3_8_Chapter.html#Fn10)

Cohort analysis is the study over time of the behavior of a group of customers that share a common characteristic. These behaviors are any that are of interest to the marketer, and studying them enables the identification of relationships between a population’s (cohort’s) characteristics and its behavior. Cohort analysis provides that often elusive “apples-to-apples” comparison between groups of customers. The literature on cohort analysis frequently uses university graduation year as an example of a cohort. Each graduating class is a cohort, and characteristics are tracked and compared between cohorts to determine, for example, if there is a relationship between a cohort’s graduation year and its average income.

Cohort analysis is important because for key business or marketing metrics, a cohort analysis reveals how those metrics change over the life of a customer relationship. These revelations lead to better customer retention and more precise estimates of customer lifetime value (CLV). Knowing CLV with precision is important, because it guides decisions about how much a firm should invest to acquire new customers. This approach has been embraced by app and game developers, online, e-commerce, and software as a service businesses, where its use is particularly relevant. Besides CLV analysis, some common cohort analyses include daily/weekly/monthly sales, new versus repeat sales, average order value, and time between purchases.

The tools for building cohort analyses include many previously referenced BI and CI solutions, as well as CoolaData, Flurry, Google Analytics, KISSmetrics, Kontagent, Mixpanel, RJMetrics, and others. Cohort.ly and USERcycle both offer a level of free cohort analysis services. When selecting a tool, make sure it provides an Application Program Interface (API) to or otherwise integrates with the environments you want to include in your analysis, and that it is easy to include historical data that is important to your analysis.

### Operations and Logistics Marketing Analytics

Analytics tools and solutions that fall within the operations and logistics realm have much to do with an organization’s digital presence. Where once this digital presence was simply the company website, now it encompasses every digital touchpoint: the website, micro-sites, all company-curated social media, search engine optimization (SEO), search engine marketing (SEM), and mobile. Marketers need tools that allow them to understand not just the individual performance of each of these separate digital channels but to have a holistic view of their performance.

The place to begin this discussion is with the website and the analytics tools that help manage and optimize it. What is true with increasing frequency is that buyers investigate potential vendors online, going deep into the purchase consideration process before vendors are even aware of potential buyer interest. For this reason, the website is still ground zero for digital marketing efforts for most organizations. A company whose website does not easily and quickly provide the content potential buyers need will suffer because of it. Tools are needed that give companies real-time insight into how visitors are interacting with their websites, so that companies can quickly pivot to address website and content deficiencies.

The tool that dominates the marketing analytics landscape is Google Analytics. Many marketers got their first exposure to analytics through the use of this tool. Stating that Google Analytics dominates the landscape is not mere hyperbole: it is used by half of all the websites, and as a web analytics tool, it enjoys a market share of 82 percent.[^(11)](#A978-1-4842-0259-3_8_Chapter.html#Fn11) If a marketing organization had to limit itself to the use of just a single analytics tool, Google Analytics would be that tool. If having a website that performs well is critical to an organization’s success, using Google Analytics to manage that website makes tremendous sense for one reason alone: about two-thirds of all US search traffic goes through Google.[^(12)](#A978-1-4842-0259-3_8_Chapter.html#Fn12) If the goal is for visitors to easily find your website through a Google search, why not use the analytics tool the search giant provides?

There are many things about Google Analytics that make its use so compelling. It’s free for anyone who wishes to use it, it’s easy to implement and use, it has great reporting features, and Google is constantly adding features that make it more valuable and extend its capabilities. It’s a must-have tool for any organization that considers its web presence important, because it reveals information about visitors, traffic, and content to which an organization would otherwise be blind. Here are some of the things a user of Google Analytics can learn:

*   Visitors: the number of visitors to the website, how many were unique, how much time they spent on the site, how many were first-time and returning visitors, and more. Demographic data about visitors is also available, such as their geographic location (country and city), and which browser and device they were using when they visited your site.
*   Traffic: Google Analytics will tell you how much traffic your website is getting, but more important, it can reveal how those who visit your website got there. As the paths to a website get more diverse, it is important for marketers to understand the source of their web traffic, specifically how much came from search engines and other referral sources. Regarding search engine traffic, Google Analytics can also tell you the keywords that are responsible for generating traffic to a website, information that is critical to performing SEO.
*   Content: through Google Analytics, marketers can learn how visitors are consuming their web content. The web pages that are entry and exits points are recorded, as well as how many times a visitor viewed a page and how long they stayed there. This information helps marketers optimize and improve the SEO characteristics of their ­content. Google Analytics can even provide statistics about how long it takes pages to load, helping identify and correct slow-loading pages.

The data on website visitors, traffic and content just scratch the surface of Google Analytics’ capabilities. It is a powerful tool that grows stronger with each subsequent release. In 2013, Google offered Universal Analytics, adding features that enable tracking users across multiple devices. With Google Universal Analytics, the ability exists to collect metrics from any Internet-connected device that can send an HTTP data request. The implication of this connectivity is that a very diverse set of devices, such as game consoles, various apps, and even Internet-connected Blu-ray players can share usage data with Google Analytics.

Google Analytics is the gorilla in the digital analytics space, but other vendors exist as well, and their solutions merit consideration. TrustRadius has published analysis in the form of TrustMaps for Digital Analytics software,[^(13)](#A978-1-4842-0259-3_8_Chapter.html#Fn13) in which analytics solutions are ranked on the basis of user ratings and adoption within small, mid-size, and enterprise market segments. Marketers that are considering implementing analytics technology will find this type of analysis very helpful as they attempt to understand the array of offerings in the market.

A company’s digital presence in this modern marketing era is no longer limited to just its website but includes its social media and mobile presence as well. Any approach to analytics needs to span the entire digital waterfront, including social and mobile. For some organizations, social media has eclipsed the website in importance. Google Analytics certainly provides some social reporting capabilities, and a cadre of more specialized solutions also provide insight into brand conversations that occur in the social realm.

Social media tools are necessary for identifying and participating in the conversations about a brand or company that take place in the social realm. These solutions help monitor those conversations, sifting through the seemingly infinite number of social media posts and detecting those you need to know about because they relate to your brand. Simply knowing about these conversations enables participation in them. For example, an individual who rants about a poor service experience with a brand may now see that brand respond as part of an effort to recover from a service failure.

Beyond just providing opportunities to address social media complaints, these tools allow brands to measure sentiment about the brand, its products, or what it represents. They provide users with the ability to do more than simply count likes, follows, or shares and instead measure the true level of engagement a brand has with its followers. Many tools fall into this social media monitoring (also known as social media listening) and analytics category, including HootSuite, Netbase, Radian6 (by Salesforce), SproutSocial, Sysomos, and numerous others. Some solutions are free, so budget should not be a reason that marketers aren’t managing and measuring their social media presence.

The same discussion about social media applies to the mobile channel. If mobile is part of an organization’s media mix, then that organization needs to ensure it is included in the marketing analytics portfolio. Mobile is one of the fastest growing marketing channels, providing the ability to exploit GPS-enabled mobile devices to gather geographic and behavioral data about customers. This data can enable a business to detect the proximity of a customer and push coupons, sales alerts, or other offers to that customer.

## Conclusion

This chapter has revealed the tip of the iceberg when it comes to marketing analytics tools. There are many from which to choose, and it’s not uncommon to see firms with a mature marketing analytics process use five or more of them simultaneously. When this is the case, it becomes even more important to have a dashboard (as discussed in Chapter [7](../Text/A978-1-4842-0259-3_7_Chapter.html)) presenting a comprehensive view that makes it easy to access and understand all the important information the marketing analytics process is producing. If BI tools are in use, they will enable the development and publication of a dashboard. There are also vendor solutions specific to this purpose of creating an analytics dashboard, such as Birst, Cyfe, Domo, iDashboards, and QlikView. Free solutions also exist. For the many reasons described in Chapter [7](../Text/A978-1-4842-0259-3_7_Chapter.html), a dashboard is an important communication and process management tool for the marketing analytics process.

This discussion of tools and solutions that support strategic, operational, and logistical marketing analytics just begins to touch on all the technology available to modern marketers. When it comes to selecting tools, the set of choices is bewildering. How can marketers make the right choice? As this book has consistently advised, start with the objectives. Knowing the objectives narrows your search for tools and technology. Next, go online and research candidate providers, placing emphasis on those that have already created the type of success you want to replicate. As you consider the options, prioritize ease of use. Finally, evaluate the solutions on your short list to make a selection. Most vendors provide free trials so you can see how an analytics tools works in your environment, with your data.

Footnotes [1](#A978-1-4842-0259-3_8_Chapter.html#Fn1_source)

From a speech to announce a grant, February 17, 2005, [http://news.microsoft.com/2005/02/17/steve-ballmer-aacis-unlimited-potential-grant-announcement/](http://news.microsoft.com/2005/02/17/steve-ballmer-aacis-unlimited-potential-grant-announcement/) .

[2](#A978-1-4842-0259-3_8_Chapter.html#Fn2_source)

“Marketing Automation: Insights, Landscape & Vendor Analysis,” Demand Metric, Clare Price & Kristen Maida, April 2014, p. 4; [http://​www.​demandmetric.​com/​content/​marketing-automation-solution-study](http://www.demandmetric.com/content/marketing-automation-solution-study).

[3](#A978-1-4842-0259-3_8_Chapter.html#Fn3_source)

“Digital Marketing for 2015: Targeting Audiences & Adopting New Strategies,” Demand Metric, November 2014, p. 9.

[4](#A978-1-4842-0259-3_8_Chapter.html#Fn4_source)

“Content Preferences Survey Report,” DemandGen, May 2012, p. 5; [http://​www.​slideshare.​net/​G3Com/​content-preferences-surveyfinal](http://www.slideshare.net/G3Com/content-preferences-surveyfinal).

[5](#A978-1-4842-0259-3_8_Chapter.html#Fn5_source)

J.M. Culkin, “A schoolman’s guide to Marshall McLuhan,” Saturday Review, March 18, 1967, pp. 51–53, 71–72.

[6](#A978-1-4842-0259-3_8_Chapter.html#Fn6_source)

See `https://www.gartner.com/doc/2668318/magic-quadrant-­business-intelligence-analytics`.

[7](#A978-1-4842-0259-3_8_Chapter.html#Fn7_source)

See [http://​www.​gleanster.​com/​vendors/​customer-intelligence](http://www.gleanster.com/vendors/customer-intelligence).

[8](#A978-1-4842-0259-3_8_Chapter.html#Fn8_source)

See [http://​www.​agilone.​com/​definitive-guide-to-predictive-analytics-lp](http://www.agilone.com/definitive-guide-to-predictive-analytics-lp).

[9](#A978-1-4842-0259-3_8_Chapter.html#Fn9_source)

“Watch This Space: Big Players Gobble Up Attribution Solutions,” Demand Metric, May 16, 2014, [http://​blog.​demandmetric.​com/​2014/​05/​16/​watch-space-big-players-gobble-attribution-solutions/​](http://blog.demandmetric.com/2014/05/16/watch-space-big-players-gobble-attribution-solutions/).

[10](#A978-1-4842-0259-3_8_Chapter.html#Fn10_source)

See [https://​www.​forrester.​com/​The+Forrester+Wa​ve+CrossChannel+​Attribution+Prov​iders+Q4+2014/​fulltext/​-/​E-res115221](https://www.forrester.com/The+Forrester+Wave+CrossChannel+Attribution+Providers+Q4+2014/fulltext/-/E-res115221).

[11](#A978-1-4842-0259-3_8_Chapter.html#Fn11_source)

“Usage of Traffic Analysis Tools for Websites,” W3Techs, March 1, 2015, [http://​w3techs.​com/​technologies/​overview/​traffic_​analysis/​all](http://w3techs.com/technologies/overview/traffic_analysis/all).

[12](#A978-1-4842-0259-3_8_Chapter.html#Fn12_source)

“comScore Releases January 2015 U.S. Desktop Search Engine Rankings,” February 19, 2015, [http://​www.​comscore.​com/​Insights/​Market-Rankings/​comScore-Releases-January-2015-US-Desktop-Search-Engine-Rankings](http://www.comscore.com/Insights/Market-Rankings/comScore-Releases-January-2015-US-Desktop-Search-Engine-Rankings).

[13](#A978-1-4842-0259-3_8_Chapter.html#Fn13_source)

TrustRadius, “Top Rated Digital Analytics Products for Small Businesses, Mid-size Companies and Enterprises,” September 3, 2014, [https://​www.​trustradius.​com/​articles/​top-rated-digital-analytics-products](https://www.trustradius.com/articles/top-rated-digital-analytics-products).

# 9. Becoming Data Driven

Weaving Analytics into the Fabric of Marketing

Having knowledge of something that needs to occur does not ensure it will get done. Just understanding the need for and value of doing a certain thing is not enough to guarantee it will happen. For example, most people understand that dietary changes and exercise are the keys to a healthier lifestyle and better fitness. In fact, they can have in-depth knowledge of exactly what changes are required over what period of time to produce those changes. But actually making those changes is another matter entirely. It takes will and discipline to sustain an effort that achieves results; regular encouragement is also helpful, because it is hard to change deeply engrained habits and thinking.

Adopting and committing to a marketing analytics process has many parallels to a pursuing a healthy lifestyle. It is possible to gain the knowledge, proficiency, and skills to succeed with marketing analytics and still fail. Understanding the precepts, accepting the wisdom, internalizing the recommendations, and believing the advice presented in this book or from other sources on marketing analytics is necessary for success. However, these things are not enough to realize the hoped-for change from making the effort to become a data-driven marketing organization through the use of analytics. The gains and improvement that are possible through marketing analytics are the result of embedding its processes deep into the culture of marketing and the rest of the organization. In other words, the analytics process can’t just be a patch sewn onto the marketing cloth to hide a flaw—it must be a series of threads woven directly throughout the fabric so analytics become integral, even indistinguishable, from what marketing does.

Note

Marketing analytics has its full impact only when it is completely integrated into the operations and culture of marketing.

The pursuit of success with marketing analytics isn’t just a matter of adding the analytics process to what marketing is already doing. Success requires changing the culture, and the resistance to cultural change is often the most difficult obstacle to overcome. Full cultural acceptance occurs when there is an organizational determination to pursue and succeed with marketing analytics. It’s characterized by a long-term perspective, the will to continue even when the results aren’t good or the process challenges are difficult, and a resolve to follow the process wherever it leads. Conceptually, it is quite simple to understand that these factors are critical to success with marketing analytics, but that doesn’t make it easy to build and sustain a successful process. A number of challenges stand in the way of spinning up a marketing analytics initiative and maintaining its momentum.

To increase the chances of success with marketing analytics, organizations cannot be naive about the challenges that come with venturing into this territory. This chapter discusses some of these challenges with the goal of creating awareness, so that with awareness comes the ability to avoid or at least better manage these challenges. In truth, these challenges are not fully avoidable. Any organization that attempts to take a serious approach to marketing analytics is going to encounter challenges at some level. Knowing what they are in advance minimizes the discouragement that can cause marketers to abandon their efforts. The foreknowledge also helps marketers prepare to deal with them; it’s far easier to avoid risks and dangers when you know of them in advance.

Challenges that marketing teams are likely to face when attempting to instill analytics into the marketing psyche include lack of clear direction, lack of support, lack of enthusiasm, and lack of freedom. These challenges are on top of the skills, tools, or data challenges discussed in previous chapters. These are broad areas of challenge, any one of which has more than enough potential to sink an analytics initiative. The rest of this chapter is concerned with exploring these areas of challenge in greater detail and suggesting strategies for mitigating them.

## Lack of Direction

> If you don’t know where you are going, you might wind up someplace else. —Yogi Berra

Marketing analytics, a business process that provides tremendous direction for the marketing team, can itself suffer from a lack of direction. This lack of direction is the result of failure to articulate the vision for embracing ­analytics within the marketing function. Too often, leaders simply assume that the vision for doing something is clear to everyone, when in fact it is not. Other times, leaders fail to understand how important clarity and unity of vision is to helping any initiative succeed. The higher the stakes of an initiative, the more important it is to ensure that everyone buys in to the vision for doing it. This is the first thing that must happen on the journey to success with marketing analytics.

When pursuing success with marketing analytics, having a vision for doing it is the first important ingredient in the recipe. But defining, clarifying, and communicating the vision is often sacrificed for the sake of expediency. Failing to spend adequate time to sort out the vision is a fatal oversight. In the metaphorical journey that marketing embarks on when adopting analytics, the vision is the destination. Whatever marketing hopes to achieve through analytics is the substance of the vision. The absence of a compelling vision for marketing analytics puts marketing at risk of winding up someplace other than where it intended, to paraphrase Yogi Berra.

It is the job of the CMO to cast the vision for anything marketing does, and this certainly includes analytics. It is entirely possible for a marketing team to throw itself headlong into an analytics initiative without a vision to drive the effort. Marketing analytics is, after all, a great idea. It’s very easy for everyone to agree that it is something “we should be doing.” However, what is the purpose of the effort? What goals does the team have in view for the analytics process? How will the team allocate resources and use the data the process produces? How will the team even know what kind of data to collect and analyze with the process?

As this book has discussed, analytics is not some extracurricular activity the marketing team pursues when it has time or feels like it. Analytics becomes the hub of how marketing thinks, plans, and operates. But without a vision, what happens when there are obstacles? What motivates the team to keep up the effort when the work becomes difficult? In the absence of a vision, the answer to these questions is “nothing.” A marketing analytics process that isn’t driven by a compelling vision is a short-lived enterprise. The moment it is no longer convenient or easy to stick with the analytics process, it is abandoned. There will surely be moments where analytics are no longer convenient or easy.

What’s the right vision for marketing analytics? It’s increasingly common to see marketers turn to analytics to help transform marketing into a true revenue-generating function. Other marketing teams wish to minimize or eliminate the effect of internal politics on how marketing operates and its budget. These examples are just two of many that can give the initiative some direction. Each CMO must determine the right vision for propelling the marketing analytics function. They must regularly communicate it with clarity so that everyone on the marketing team, as well as the external stakeholders, knows where ­analytics are taking the team.

Note

CMOs must determine the right vision to drive the marketing analytics effort and regularly communicate it with clarity to everyone on the marketing team.

As important as vision is to providing direction to all that marketing does, including the analytics process, it gets its direction from values. What are the non-negotiable values on which the marketing analytics function should rest? The analytics need to stay firmly tethered to those values and pointed in the direction of the vision. An example of a value associated with marketing analytics is an “analytics first” approach to every marketing initiative. When this value exists, one of the first steps in planning any marketing initiative is the identification of metrics to measure it. Those metrics should serve as the final arbiter of how an initiative is doing.

These values really determine how seriously marketing will take analytics. Therefore, when a marketing team is first embarking on an analytics initiative, it should take the time to determine its values. For marketing teams that already have an analytics process but haven’t expressed values to guide it, it’s never too late to put some in place. Here are 10 representative values that could serve as guides for any marketing analytics initiative:

Every marketing initiative will begin with the determination of success metrics. We will not use or report metrics based on inaccurate or incomplete data. We will not publish “vanity” metrics just to make marketing look busy or because someone likes to see marketing numbers. Everyone has an opinion, but metrics are the final arbiter when determining results or success. When the analytics output conflicts with conventional wisdom, we’ll believe the analytics. When the analytics reveal good results, we’ll proudly take credit. When the analytics reveal poor results, we’ll take responsibility. We will not use the output of our analytics process punitively, only to improve. Analytics don’t make our decisions for us, but they will inform the decisions we make. Every marketing initiative will end with reviewing the success metrics.

Having a vision to provide direction to the marketing analytics process, combined with values to guide how the process and its output will work, go a long way toward integrating analytics into the fabric of marketing. Even with this in place, other challenges remain.

## Lack of Support

> Adversity is the first path to truth. —Lord Byron

It is naive to believe that just because something is a good idea, everyone will support it. Marketing analytics is certainly a good idea, but the lack of support from in and outside the marketing department may catch many supporters off guard. When pursued to a mature level, analytics will transform a marketing department into a data-driven entity, for which most of the doubt about its performance is vanquished. Who wouldn’t want this? As Chapter [1](../Text/A978-1-4842-0259-3_1_Chapter.html) describes, most of the marketers in organizations that are flying blind with respect to analytics, or within organizations using pseudo-analytics, are either not receptive to or are just feigning interest in them. These kinds of marketing organizations probably don’t want an analytics initiative thrust on them, but everyone else would, right? Not necessarily.

Chapter [2](../Text/A978-1-4842-0259-3_2_Chapter.html) contains a discussion of marketing analytics challenges, summarized in Figure [2-7](../Text/A978-1-4842-0259-3_2_Chapter.html#Fig7). This issue of support, either from the executive team or the marketing team, doesn’t rank very high on the list of challenges. It’s therefore important to clarify why this chapter revisits this topic in much more detail. Support for marketing analytics, or lack thereof, can come in two forms:

Support for having a marketing analytics initiative: the subject of Chapter [2](../Text/A978-1-4842-0259-3_2_Chapter.html). Support for or belief in the output of the marketing analytics process: the subject of this chapter.

Here’s what every marketing analytics supporter wants to believe: the output of a valid marketing analytics process based on accurate data trumps all other beliefs, biases, superstitions, and opinions. In other words, simply putting the metrics in front of nonbelievers should immediately spur acceptance of the logical conclusions of those metrics. After all, the data is the data. The data represents irrefutable fact. How is it even possible to dispute it? Yet it happens with regularity: logic-defying resistance to metrics that frustrates ­analytics supporters.

This phenomenon isn’t unique to marketing analytics acceptance, support, and belief. It doesn’t matter whether the discussion is about climate change, the link between vaccines and autism, or marketing analytics: previously held convictions don’t just easily give way to cold, hard facts. In a famous case study in psychology, Stanford University psychologist Leon Festinger stated, “A man with a conviction is a hard man to change. Tell him you disagree and he turns away. Show him facts or figures and he questions your sources. Appeal to logic and he fails to see your point.”[¹](#A978-1-4842-0259-3_9_Chapter.html#Fn1) Marketers must understand and prepare for the likelihood that the measurements and data produced by their ­analytics process may not enjoy automatic and instant acceptance. Becoming data driven and weaving analytics into the fabric of marketing can’t occur until such bias is dealt with.

Note

Marketers must prepare for the possibility that the metrics and insights from their analytics process may not enjoy immediate acceptance.

Anyone who has ever uttered the phrase “why won’t you listen to reason?” in an argument understands the frustration of dealing with someone who discounts or ignores facts. There’s some interesting science behind this phenomenon: “Reasoning is actually suffused with emotion (or what researchers often call ‘affect’). Not only are the two inseparable, but our positive or negative feelings about people, things, and ideas arise much more rapidly than our conscious thoughts, in a matter of milliseconds—fast enough to detect with an EEG device, but long before we're aware of it.”[²](#A978-1-4842-0259-3_9_Chapter.html#Fn2)

Data and information that negatively reinforces beliefs doesn’t have smooth sailing to acceptance in the brain of the recipient. When confronted with data that doesn’t conform to beliefs, often it isn’t reasoning that occurs but rationalization. Consider this scenario: an older business owner believes that traditional print media advertising is the best way to reach customers. “We have to be in the newspaper” is the edict from this owner. This owner’s marketing director runs both traditional print and digital media campaigns, tracking key metrics; these metrics prove that digital far outperforms traditional print. However, the owner doesn’t agree with the data, and instead allows personal bias to prevent the marketer from diverting funds away from traditional print to invest more heavily in digital.

In this scenario, the marketer does exactly what should occur: identifying success metrics and using them to monitor results, then making improvement actions. The business owner won’t hear of it. What is happening in this scenario has been experienced by many a seasoned marketer, and its occurrence isn’t limited to just marketing or analytics. “Our ‘reasoning’ is a means to a predetermined end—winning our ‘case’—and is shot through with biases. They include ‘confirmation bias,’ in which we give greater heed to evidence and arguments that bolster our beliefs, and ‘disconfirmation bias,’ in which we expend disproportionate energy trying to debunk or refute views and arguments that we find uncongenial.”[³](#A978-1-4842-0259-3_9_Chapter.html#Fn3)

Scenarios like this reveal how truly difficult it is have complete objectivity about data. All data has to pass through a filter of understanding consisting of our biases and paradigms. We can’t easily suspend these because most of the time we don’t even realize we have them. Even when we do have awareness of them, we can’t just turn them off. The result is that even with the best of intentions we easily slip into rationalization mode to shade conclusions toward our own bias. This rationalization bias can come from any quarter. The example scenario presented here is of external bias affecting how marketing operates, but bias can just as easily come from within the marketing team.

What can marketing analytics advocates do to avoid this frustration? It isn’t entirely avoidable, but it is possible to minimize or counter the effects of bias:

*   Recognize that it exists. Like death and taxes, the presence of bias affecting the marketing analytics process is inevitable for most marketers. This bias against analytics is a feature of the marketing landscape (and many other landscapes).
*   Don’t be surprised by it. Being indignant because bias impedes progress or acceptance of marketing analytics is an understandable response, but it gets you nowhere. Plan to encounter it and diligently work through it.
*   Know who your allies are. While there are usually a few people with bias, there are also supporters of the analytics effort. Take a census to understand the views of the stakeholders, and then leverage the influence of your allies.
*   Use the culture to your advantage. Culture can create lift or drag for marketing analytics—sometimes both! If there are elements of culture or specific core values that support the marketing analytics process, don’t hesitate to use them.
*   Think small. When the response to the marketing ­analytics process is hostile, and it comes from a source with veto power, prepare to retreat temporarily. In the face of such opposition, don’t attempt to use analytics to justify major marketing changes, because those efforts will fail. Instead, temporarily limit the application of analytics to smaller matters.
*   Avoid outright confrontation. It’s tempting to turn efforts to instill marketing analytics and a culture of acceptance into a crusade, but avoid doing so. Allowing the emotion around analytics to escalate to overt hostility simply causes opponents to dig in and resist with more vigor. Even when you face resistance, keep the olive branch extended.
*   Maintain a long-term perspective. Don’t let resistance and a few setbacks discourage your efforts to engrain analytics into the marketing and corporate psyche. Mentally prepare for progress that often occurs in a “two steps forward, one step back” type of rhythm. Eventually, marketing analytics will prevail.
*   Appeal to a higher power. If one of your allies is the boss, president, CEO, or owner, consider enlisting that person to help flatten the internal barriers to the analytics acceptance. This, however, is sometimes a delicate operation, as it is easily viewed as “going over someone’s head,” a move that is rarely appreciated and can burn some bridges.

Experiencing resistance to marketing analytics can make one feel like Galileo, who was persecuted for having the audacity to claim the Earth orbited the Sun. Nothing is more frustrating than having the facts and truth on your side and being met with stubborn, irrational resistance. The truth is like the force of water, capable of carving deep channels through the hardest rock. Persistence in pursuing an analytics initiative will ultimately yield the same result, eroding away the resistance to the truths it reveals.

## Lack of Enthusiasm

> The real secret to success is enthusiasm. —Walter Chrysler

It’s not unrealistic for an organization to have a welcoming culture and intellectual agreement with marketing analytics, but still fail to see an analytics initiative get traction and thrive. When a prosperous company hasn’t fully embraced marketing analytics, even though it is not opposed to them, more often than not the reason is because there’s little enthusiasm for doing so. To some of these organizations, analytics seems like unnecessary busywork. Because of the prosperity it is experiencing, there’s usually an “if it ain’t broke, don’t fix it” attitude. Marketing analytics is relegated to back-burner status, something that the company will get around to doing when it isn’t so busy or if it becomes really necessary.

A crisis becomes the catalyst for the adoption of marketing analytics in these companies. Organizations that feel as if they are doing fine simply don’t have the motivation to seriously pursue marketing analytics. Weaving analytics into the fabric of marketing requires a mature view of analytics as a full-time commitment, something that occurs in lean and prosperous times. An effective marketing analytics process softens the lean times and magnifies the prosperous ones, smoothing out the economic peaks and valleys.

It’s easy to understand this lack of enthusiasm for marketing analytics. What’s behind it is an attitude that parallels the view of a reasonably fit person toward exercise: there’s no compelling need as long as that person feels fine. It’s the same for companies: as long as business is reasonably good, marketing ­analytics is not a priority. There’s no disagreement about the worth of analytics, but neither is there enthusiasm for the effort involved in pursuing an initiative. The challenge is similar to that of convincing young people of the need to buy life insurance. Those with the ability to look ahead and think strategically can anticipate a day when they’ll be glad they bought life insurance. In many ways, marketing analytics is like an insurance policy, protecting marketing from the worst possible outcomes by showing it the best path to prosperity.

Note

When business is good, organizations may have no enthusiasm for pursuing an analytics initiative.

There’s a complacency that comes with even small degrees of corporate success. It can take hold in companies that are not achieving their objectives but perceive that they’re making progress toward them and can easily rationalize that they’re on the right path. If that path doesn’t include marketing ­analytics, it is unlikely that they will add it for a number of reasons: it’s work, they don’t understand it, they don’t know where to begin, what they’re doing now seems to be working, there are too few resources that are better invested in other activities. The list of reasons (excuses) is almost endless. It’s easy for the marketing team or the C-suite to talk themselves out of pursuing an analytics initiative.

What is ideal is for organizations, while healthy and growing, to commit to marketing analytics, gaining proficiency in the process and use of metrics during a time of strength. This kind of marketing analytics genesis works well because the process hopefully matures fast enough to help insulate the company from revenue downturns. The company that follows this path can identify alarming trends faster, have greater agility in responding to them, and do so with more authority and conviction because of the analytics process. This is the ideal scenario for birthing a marketing analytics process, but the barrier that can prevent it from occurring is satisfaction with the status quo.

It’s more likely that some crisis will cause the hasty birthing of a marketing analytics process. When this happens, there is some underlying level of desperation, and with desperation comes a lack of patience. Analytics created out of these circumstances can ultimately succeed. But it takes strong leadership to take a novice organization and have it stay the course when it is looking for instant gratification from analytics. The risk here is that what is a very good idea—marketing analytics—gets abandoned when it doesn’t immediately prove to be the salvation for a company’s woes. This impatience is exactly the reason companies should embrace marketing analytics when they don’t think they need them, so they’ll be able to leverage them when they are needed most.

As this book has described, there is a period of time required for learning, identifying proper metrics, collecting the data, and becoming proficient in its analysis. It is possible to gain some immediate benefits from marketing ­analytics, but to have the practice mature and deliver all the benefits takes time. If it takes a crisis to cause a company to embrace marketing analytics, so be it. The right view of the process is as a strategic initiative, not a tactical bandage. Marketers should take advantage of any opportunity that opens the door for analytics and move through that opening without looking back.

When complacency and satisfaction with the status quo are the issue, here are some things marketers can do to generate enthusiasm for analytics and start weaving it into the fabric of marketing:

*   Accentuate the positive. While analytics can help prevent a company from going over the cliff, they can also help it climb the mountain. In the early going, emphasize metrics and analysis that lead upward. This advice in no way is meant to imply that marketing should bury its head in the sand if metrics deliver bad news. But in the early going, using metrics that drive improvements others care about generates enthusiasm.
*   Come bearing gifts. Analytics data can provide invaluable insights. Marketers can share that data with those it needs as allies, helping them gain insights that are otherwise inaccessible. The caution here is not to let analytics data become a political tool, as ideally there is complete transparency throughout the organization with respect to this data. But those who aren’t familiar with how helpful such data can be should receive the gift of marketing analytics data with appropriate explanations and assistance in interpreting it.
*   Practice internal public relations. It’s unreasonable to expect the organization to connect all the dots where the analytics process is concerned just from seeing the data it produces. In other words, you can’t just throw data at the organization and expect them to appreciate it. Champion the process internally by intentionally promoting its successes, even the smallest ones.
*   Maintain the drumbeat. Be persistent in encouraging the practice of marketing analytics. Do this by touting the benefits, not constantly warning of the potential dire consequences of not doing so. Fear is a motivator, but not the best one.

The worst-case scenario for organizations without enthusiasm for marketing analytics is a crisis that triggers interest in them. Marketers that are not having success putting analytics on the corporate radar screen can do some background preparation, knowing that a day will come when the call goes out for the kind of help that analytics can provide.

## Lack of Freedom

> The truth will set you free, but first it will piss you off. —Gloria Steinem

Is marketing more art or more science? This question has fueled plenty of debate in and outside of marketing circles for decades, and the intensity of the debated hasn’t diminished. Attempts to weave analytics into the fabric of marketing have run headlong into this debate before, and the result was often the failure of the initiative. This chapter’s previously discussed barriers of lack of direction, support, and enthusiasm can all originate from inside or outside the marketing department. The barrier of lack of freedom, however, almost always comes from within the marketing department.

In this context, freedom refers to creative freedom. Whenever there is discussion of limiting someone’s freedom, the reaction is usually swift and harsh: “over my dead body” or some similarly strong invective. Analytics are viewed as a set of shackles, constraining marketing to some cold, clinically determined path that some perceive as boring and uninspired. This is an irrational view, of course, but that doesn’t prevent some from reacting this way to the “threat” of having analytics forced on them.

The goal of this book isn’t to resolve once and for all the debate over whether marketing is science or art, but it will take a position: marketing is both. Receive with skepticism anyone who says it is all one or the other. The truth is, these two poles of marketing, although seemingly opposite, need one another and are both necessary for marketing to flourish. In fact, far from opposing one another, the creative and analytic personalities of marketing are very complementary. Effective CMOs understand the need for balance between these forces and intentionally manage them to proper levels to drive the maximum marketing contribution.

Note

The science and creative elements of marketing aren’t in conflict with one another, but complement each other: both are necessary for marketing to flourish.

Imagine marketing that was almost entirely a product of analytics, with creativity playing a minor, supporting role at best. This scenario conjures up thoughts of unspectacular marketing assets that produce consistent, predictable, and incremental results from such an effort. The metrics are in the driver’s seat determining what marketing does.

On the other end of this spectrum, imagine marketing that is entirely a creative venture, with analytics playing the minor supporting role. Here the results are more hit or miss. The assets are almost always dazzling, innovative, and even edgy. But these assets don’t always work. Sometimes the outcome of creativity is a smash hit, and other times a dud. Here marketing is riding the results roller coaster, experiencing plenty of highs and lows. Marketing vacillates between being the hero and the scapegoat. Creativity is in the driver’s seat determining what marketing does.

Neither of the scenarios in the previous two paragraphs represent the place marketing should live. Historically, marketing has lived much more on the creative side of the spectrum, and that is one reason the greatest resistance to analytics often comes from within the marketing department. If the truth be told, creativity been used to deflect the pressure to adopt analytics within the marketing function. George Torok, marketing expert and speaker, weighs in with these thoughts: “I believe that many marketers try to portray marketing as art when they can't measure their results. Hence they give up responsibility for their marketing programs. They suggest that marketing is all chance. Many self-declared branding experts talk about the art of branding and refuse to face the science of measurement. Don't be fooled by that hocus pocus.”[⁴](#A978-1-4842-0259-3_9_Chapter.html#Fn4)

There is a balance and order to maintain between the analytical and creative personalities of (and in) marketing. It starts with creating the understanding that great marketing is the result of properly combining science and art. There’s actually a lot of common ground on which the adherents of marketing analytics and the creative marketing types can stand. The creative process in marketing has long been an adaptation of the scientific method, something many marketers may not realize. Their creative ideas represent a hypothesis. The development of those ideas into campaigns and assets represent experiments from which they make observations to determine if the hypothesis was true, or if there is a need for a different hypothesis. Marketing analytics can step onto this stage and contribute a significantly better understanding of how true these hypotheses are.

Marketing analytics isn’t a creativity killer. If it is, then its implementation and management is flawed. Analytics and creativity should have a symbiotic relationship in the marketing organization, where the analytics point to areas in need of more creativity, and where creativity is measured to understand the value it provides. Where analytics and creativity are concerned, the whole is greater than the sum of the parts.

As the barriers discussed in this chapter illustrate, weaving analytics into the fabric of marketing requires patience and persistence. Building the analytics process on the company’s values and vision provides the foundation and direction the initiative needs to start and succeed. Those who champion marketing analytics should expect some resistance in one or more of the areas discussed in this chapter. A patient approach that persistently emphasizes the success of analytics will ultimately win over the doubters.

Footnotes [1](#A978-1-4842-0259-3_9_Chapter.html#Fn1_source)

Leon Festinger, Henry W. Riecken, and Stanley Schachter, When Prophecy Fails: A Social and Psychological Study of a Modern Group that Predicted the Destruction of the World (Minneapolis: University of Minnesota Press, 1956), p. 3.

[2](#A978-1-4842-0259-3_9_Chapter.html#Fn2_source)

Chris Mooney, “The Science of Why We Don’t Believe Science,” Mother Jones, May/June 2011\. See [http://​www.​motherjones.​com/​politics/​2011/​03/​denial-science-chris-mooney](http://www.motherjones.com/politics/2011/03/denial-science-chris-mooney).

[3](#A978-1-4842-0259-3_9_Chapter.html#Fn3_source)

Ibid.

[4](#A978-1-4842-0259-3_9_Chapter.html#Fn4_source)

George Torok, “Marketing—Art or Science?” See [http://​www.​torok.​com/​articles/​marketing/​MarketingArtorSc​ience.​html](http://www.torok.com/articles/marketing/MarketingArtorScience.html).

# 10. The Marketing Analytics Frontier

Big Data, Predictive Analytics, and Advanced Attribution

Marketing analytics is not a static discipline that, once implemented, never changes. The reasons for doing marketing analytics don’t change much, nor do the core principles that drive analytics work. However, the approaches, techniques, and technologies involved in the practice of analytics are constantly changing. There is a marketing analytics frontier, and those who venture there are discovering game-changing insights that yield significant competitive advantages. This chapter explores some of the trails that the marketing analytics pioneers are currently blazing.

Change in the marketing analytics discipline is actually occurring at a dizzying pace. In general, this change has come in the form of innovations that are providing business and customer insights never imagined possible just a few years ago. The most cutting-edge marketing analytics practices are delivering more precise information at a greater level of detail than ever before, and this information is reaching marketers almost in real time. With this information, marketers are able to understand their customers at the deepest possible level, accurately predicting events like purchases and defections. Marketers are able to optimize their channel and media mix based on data, not intuition. The frontier of marketing analytics promises to transform marketing into an undisputed revenue center, not just a producer of campaigns and enabler of sales.

While there are many changes taking place on the marketing analytics frontier, this chapter focuses on three for which marketers can’t afford to claim ignorance: big data, predictive analytics, and advanced attribution. The rest of this chapter presents an overview of what these things are and why marketers should care about them.

Note

The marketing analytics frontier is transforming marketing into an undisputed revenue center, not just a producer of campaigns and enabler of sales.

## Big Data

> Without big data, you are blind and deaf in the middle of a freeway. [¹](#A978-1-4842-0259-3_10_Chapter.html#Fn1) —Geoffrey Moore

Understanding customers and what motivates their behavior has been a quest of merchants since commerce began. Marketing owns this mission, and marketers have traditionally studied their customers through a handful of research techniques, such as past transaction analysis, observation, surveys, and focus groups. Many organizations maintain some sort of master customer information file, often part of a larger data warehouse, that marketers can reference to study customers. This data, however, contains a finite set of information that exists in varying states of currency and accuracy. Although useful, this data gives marketers a limited understanding of customer behavior. For many, this state of customer data is their current reality: old and incomplete.

The modern consumer drops plenty of digital breadcrumbs, and if the marketer can follow them, these would reveal previously hidden patterns, preferences, and signals about their intent. Consider what is typical behavior for a consumer on any given day: they communicate via mobile phone, review and post to social media, check email, browse the Internet, make purchases, download music, and read reviews of products and merchants. Consumers who do these things create an enormous data trail that marketers can leverage, and exploiting this data is at the intersection of marketing and big data.

Big data is conceptually easy to understand. It simply is a set of data so large and varied that it defies processing and analysis through the use of standard database query tools. There is no specific size designation after which a set of data becomes “big.” In this case, “big” refers more to the complexity of analyzing it, rather than the size. Although it is also “big” in terms of size, as the sheer volume is often very large. What makes it large and complex is that it comes from several sources, and it usually isn’t a single, discrete set of data. It’s better understood as a collection of related data from many sources.

Historically, marketers have worked with data that is structured, and therefore easy to access and analyze, using tools as simple as a spreadsheet. In fact, a good way to think about structured data is as any form that will fit into the rows and columns of a spreadsheet. Sales transaction data is a good example of structured data. Almost all of the data that marketers have traditionally worked with in the analytics process is structured. Structured data certainly belongs in this discussion of big data, as it is one of the two broad classes of data that fit under the big data umbrella.

The other class of data, and the one that presents the most challenges, is unstructured data. Some examples of unstructured data include digital images, online videos, blog post comments, online product reviews, chat logs, webinars, location, sensor and GPS data, to name a few. A vast quantity of this type of data exists, and it is growing at an astonishing rate. Unstructured data contains information marketers can use to better understand customers and their sentiments, particularly if they can match this data to preexisting, structured customer data.

How can marketers access and use this data to their advantage? This application dilemma is the challenge and opportunity, because the hype and promise of big data is not about the data itself. The data is of little value without tools and applications for exploiting it. Big data essentially represents a large reservoir of potential energy. Transforming that potential into kinetic energy, to unlock the value it contains, requires analytics.

Note

The promise of big data is not about the data itself, but about the detailed insights it contains, made accessible through analytics.

Standing in the way of big data applications that provide marketing benefits are the three V’s of big data, characteristics described by Doug Laney in a 2001 META Group research note: volume, velocity, and variety.[²](#A978-1-4842-0259-3_10_Chapter.html#Fn2) These help explain why big data requires new forms of processing to allow integration of insights extracted from big data with marketing and corporate decision making.

*   Volume: The sheer amount of data that is becoming accessible to marketers, which is quite large. The opportunity of big data lies in having the capacity to process the large volume of data that exists.
*   Velocity: The rate data is created or otherwise becomes available. For example, every second, on average, about 6,000 tweets go through Twitter.[³](#A978-1-4842-0259-3_10_Chapter.html#Fn3) The challenge of velocity is having the capacity to absorb the data at the increasingly rapid speeds at which it arrives.
*   Variety: The diversity of structured but mostly unstructured data types and sources. Big data implies the ability to simultaneously take multiple sources, such as video files, images, documents, text, call center notes, and geo-location data, and form a composite, actionable view of that data.

The application of big data analytics isn’t limited to marketing, but within the marketing department, it is driving more intelligent decisions that increase marketing effectiveness, improving return on investment. Some of the ways big data is proving valuable for marketers include:

*   Greater information transparency. Information and insights that are normally locked with disparate types of data become accessible and usable for marketers. For example, by exploiting big data, marketers can more precisely segment customers, which leads to better matching of products and services to their needs.
*   Personalization. Through big data analysis, marketers can develop a deeper and more sophisticated understanding of individual customers and their buying patterns. With this understanding it becomes possible to develop and deploy highly personalized marketing approaches that better allocate marketing budgets and resources and achieve a higher ROI.
*   Predictive modeling. Build accurate models that help organizations understand how customers will respond to certain actions, such as a price change or new terms and conditions.
*   Sentiment analysis. Because big data analytics considers data from many different sources, such as social media, it is possible to discern how customers or the public feel about a company, product, or service.
*   Behavioral analysis. Using big data to analyze trends helps marketers understand how customers are responding to market conditions, sales, coupons, competitive pressures, or myriad situations as they are occurring. This analysis lets marketing refine strategies and develop messaging that resonates with customers.

These are just a few examples of how marketers can exploit big data. These benefits reveal why there is such interest in big data, and as marketers race to take advantage of analytics, they are encountering some challenges, many of them related to the three V’s identified earlier. Because there is so much data for marketers to consider and use, properly storing and managing it is a challenge. Big data analytics requires different software, tools, and skills to perform, and many marketing organizations discover they don’t have them on the team, let alone anywhere else in the organization. As organizations move to exploit big data, many are finding they have to add a technical skill set to the marketing team. Big data is taking marketing to a place it has never been before, but the promised results are causing increasing numbers of marketers to venture into this strange, new territory.

The people challenge for marketers who wish to make use of big data is a major hurdle. Big data analytics requires people with skills who have never before inhabited the marketing department. The ideal blend of skills for a big data marketing team member includes:

*   Mathematical: the skills required to build, understand, and work with what are often complex data sets and models. This is the theoretical aspect of big data analytics. These skills are most often associated with a scientist.
*   Technical: the technical proficiency to use software and tools to retrieve, analyze, manage, and store the data from a wide variety of sources. These skills are most often associated with someone in the IT department.
*   Insightful: the ability to interpret the output of big data analytics and discern the “big picture” implications, translating them into actions marketing should take. This ability is most often associated with a researcher.
*   Communications: the talent and skill of sharing results with the people who need to hear them and inspiring them to take the appropriate actions as a result. This ability is most often associated with a visionary leader.

A quick scan of this necessary skills list produces a realization: not only do people who have these skills not work in the marketing department, few people anywhere have all of these skills. These skills represent a rare combination that is not often found packaged in one person. It’s possible to find the mathematical and technical skills in one person, or the insight and communications skills together, but rarely all four. This people challenge is the greatest one for most marketers seeking to use big data. What many organizations are doing is outsourcing the mathematical and technical skills while working to develop them internally.

Note

The ideal blend of skills for exploiting big data—mathematical, technical, insight, and communications—are not generally found in the marketing department.

Another major area of challenge when marketing goes to use big data analytics is of course the data itself. Harmonizing the many data sources is difficult, because organizations generally have the data marketing wants to use in different formats, structures, and databases. Creating a single, harmonized view of it all that makes it easy for marketing to use is difficult if not impossible. On top of these challenges are concerns and issues with security and privacy, often because some of the data is stored in the cloud or in systems with vulnerabilities. In the rush to take advantage of big data, some organizations may not give enough thought to keeping it secure, and breaches are damaging, both monetarily and reputationally.

Although these risks and challenges seem daunting, they shouldn’t dissuade marketers from testing the waters of big data. All of the risks are manageable, and none of the challenges are impossible to overcome. The opportunities and advantages that big data can provide are simply too great to ignore. Marketers who want to move forward with big data should follow this action plan:

Start with a vision. Having a vision is the starting point for successfully leveraging big data analytics. A vision provides direction for the effort, and a vision that is more specific is better. An example of a good vision is understanding which customers are most likely to defect. Ideally, the vision you cast for big data analytics should align with specific business objectives. Get and build skills. Most marketing teams don’t have the in-house skills to pursue a big data analytics initiative. Before going very far down the big data path, assess your skills and determine where deficiencies exist, putting a plan in place to acquire them. Those deficiencies are usually technical skills, which are needed in the early going to design and implement an architecture for big data to support the analytics work. Use consultants and outside services to bridge the skills gap at first. Identify a pilot project. Prove the value of big data by using it to solve a real marketing problem, even on a small scale. A pilot project helps prove the business case for a longer-term investment in big data analytics and also creates the necessary internal support. It brings into clear focus the skills needed to move forward, helps define the right metrics for measuring future success, and clarifies the architectural elements needed to progress further. Design a big data architecture. Based on your vision and goals for big data analytics, map out the necessary architecture for achieving them. The scope of your architecture will include system design, data flows, timing, and data models necessary to support big data analytics in your organization. Evaluate the alternative big data tools and platforms, make recommendations, and acquire them. Present the business case for big data. The experience of a successful pilot project, a skills assessment, and an architectural plan are the necessary ingredients for creating a business case that should win easy approval for stepping up the investment big data analytics.

Big data often gets its start in the business intelligence unit of a company. Companies are using big data analytics to understand and engage customers in a way that inspires greater loyalty. They can make better, data-driven decisions, seizing the opportunity to improve marketing ROI, develop new opportunities, tailor promotions to specific target markets and help optimize marketing’s strategy.

## Predictive Analytics

> Any sufficiently advanced technology is indistinguishable from magic. —Arthur C. Clarke

Any conversation about big data and marketing often leads to the topic of predictive analytics. Although these are not interchangeable terms or processes, big data and predictive analytics go hand in hand. Predictive analytics “is the use of statistics, machine learning, data mining, and modeling to analyze current and historical facts to make predictions about future events.”[⁴](#A978-1-4842-0259-3_10_Chapter.html#Fn4) Although it’s still analytics, it’s a very different kind because it is forward-looking. All other types of marketing analytics tell us what has already happened, perhaps very recently, but still looking backward. Predictive analytics give marketers the ability to know what is going to happen with amazing accuracy. The results that some are getting through the use of predictive analytics seems almost magical.

Most of the automation and analytics that marketers use, while very helpful, provides a historical view of lead generation process performance. In fact, marketers have the ability to understand with great precision how campaigns have performed and where the bottlenecks are in the marketing funnel. What this technology can’t do is serve as a crystal ball that shows the conversion future of leads in the funnel. Marketers find themselves trying to make the most sense of the historical analytics data—looking in the rearview mirror—to squeeze the last drop of performance out of campaigns and lead-generation processes. What predictive analytics does is give sales and marketing that crystal ball: accurate predictions about which leads in the funnel will buy and when.

Predictive analytics is best thought of as “predictive intelligence” that comes alongside marketers as they invent creative ways to connect with prospects and customers through various content, promotions, and offers. It helps marketers understand the propensity to buy for the leads in the funnel, separating the wheat from the chaff from a sales perspective. Predictive analytics forecasts how well customers will respond to those efforts to influence them. It looks at the likely positive and negative outcomes of doing that, showing marketing who is least likely to respond to an offer and who is most likely to buy or respond to an offer.

Note

Predictive analytics helps marketers understand the readiness of leads in the sales funnel to buy and when.

Predictive analytics, therefore, works on both the expense and revenue sides of the profit equation. By knowing who is least likely to respond to an offer, marketing can save budget dollars by eliminating those prospects in the database from future direct mail or telephone campaigns, perhaps opting to reach out via some less expensive channel. By contrast, when predictive analytics helps marketers understand who is most likely to respond, marketing can increase revenue by presenting offers to those prospects are most likely to accept.

From the organization’s perspective, predictive analytics helps drive revenue. From marketing’s perspective, it helps marketing sustain higher levels of performance. Without predictive analytics, marketing is resigned to a trial-and-error approach to its campaigns, constantly trying to determine the best blend of campaign and media mix parameters to squeeze some incremental improvements out of its efforts. Predictive analytics can shortcut this trial and error process by enabling accurate predictions of how likely, how much, what, and when a prospect will buy.

A technology that is a powerful adjunct to predictive analytics is personalization, the dynamic customization of the digital content that is presented to visitors based on known characteristics and preferences. Consumers experience this on a regular basis when they visit sites like Amazon.com or Netflix. These companies use predictive analytics to understand the interests of their visitors and then suggest other things to view or buy. A spokesperson for Netflix described how the company employs predictive analytics: “We monitor what you watch, how often you watch things. Does a movie have a happy ending, what’s the level of romance, what’s the level of violence, is it a cerebral kind of movie or is it light and funny?”[⁵](#A978-1-4842-0259-3_10_Chapter.html#Fn5) So far, business-to-consumer marketers have done the best job of using predictive analytics, and business-to-business marketers are learning how by observing them.

The place predictive analytics begins is with the data captured in an organization’s Customer Relationship Management (CRM) and marketing automation systems. This data provides marketers with a digital profile of a prospect based on their interactions with the company’s digital outreach: web visits, email opens, and so on. This is where big data enters the picture: this digital profile is combined with publicly available data from a wide variety of sources, for example, social media posts. Comparisons are made to the characteristics of successful sales transactions, and predictions are made. Although this process is conceptually simple, predictive analytics is about adding more sources of data and analyzing all of it to better predict which prospects are signaling an intent to buy.

Given the capabilities of predictive analytics, it’s not hard to understand why marketers are embracing it. It has, however, largely been the domain of large enterprises, because it takes some resources and expertise to put the infrastructure in place. Until recently, if a marketing team didn’t have the role of “data scientist” on its organization chart, then the chances were slim that it was doing predictive analytics. Now, however, a number of solution providers are offering cloud-based services to help make predictive analytics accessible to the masses. In short, it is becoming more mainstream and easier to do.

Now is the time for marketers to understand the capabilities and benefits of predictive analytics. To embrace the intelligence it provides seems like a no-brainer, but it’s not for everyone. For example, if a company’s products are not bought via a considered purchasing process—one that advances through the stages of need awareness, discovery, consideration, and decision—then predictive analytics is not a great fit. In other words, for impulse-purchased products, predictive analytics won’t provide much value. When a company’s solutions are acquired via a considered purchase process, there are still readiness issues to consider. Chief among them is the cultural readiness necessary to commit to and get success from predictive analytics. Analytics-averse organizations are far from ready to take the plunge; a data-driven orientation is a prerequisite for success.

## Advanced Attribution

> Think of attribution as the peanut butter to analytics’ jelly. Yes, each is great on its own, but for many, they’re even better together. —Natasha Moonka and Bill Kee

An ongoing challenge for marketers is proving how their efforts are responsible for generating business results. Marketers intuitively understand that their work bears some fruit, but the degree to which it does is often hazy because it is hard to know with precision how campaigns, advertising, and promotions blend together to produce results. If marketing ran a single campaign using a single media channel, then the task of attributing business results to these efforts is simple. But modern marketing takes place in a complex arena, with an intricate customer journey that is largely hidden from the marketer. It occurs across multiple devices and through mixed media types delivered through multiple channels. How can marketing know with any certainty which part of that media stream produced the result? Marketers know that some part of the stream of media it produces has influence, but which part? The knowledge of how to accurately attribute results to efforts would provide a powerful means of optimizing marketing efforts.

Back in the pre-Internet days, marketers could drop a direct mail piece to prospects and wait for responses. It was easy to match responses to the mailing and determine the conversion rate. The analytics to determine cost per customer acquisition and the ROI for the campaign were also simple. It wasn’t difficult to know what to attribute campaign conversions to, because the direct mail piece was the only element in the media stream that could produce it.

Today, the Internet is the primary marketing platform, and the number of online channels available to marketers continues to proliferate. Marketers use many of them simultaneously, and the more they use, the more difficult it is to get clarity about which components are most responsible for conversions and which ones are not pulling their weight. What is known is that the collective influence of the marketing media stream produces conversions. But which part did the best job of creating awareness? Which was best at triggering the purchase? The fallback position for many marketers has been simply to assign full conversion credit to the last piece of media in the stream, no matter how diverse the media stream was. This “last click” method of attribution, however, is imprecise.

Marketers need to know much more about how all their media perform so they can optimize the whole stream. Simply attributing credit for conversion to the last clicked element in the media steam is a wholly inaccurate approach, because the volume of media in use by most marketers is substantial and ­varied. The longer the media stream and the more varied it is, the less likely that last touch attribution accurately reports which part was most responsible for a conversion. The variety of channels makes accurate attribution difficult, and further complicating the attribution dilemma is the way customers consume content across multiple devices. A sales transaction may start online through a computer but end up being completed on a mobile device.

To understand this challenge, consider the scenario in which a consumer sees paid search media on her computer at work, leading her to visit a seller’s website, where she downloads a free buyer’s guide. The next day, that consumer receives an email from the seller with a discount offer, and as she is riding the train home from work, she uses her mobile phone to click on the offer, investigate it, and ultimately make a purchase. In this scenario, which element of the media stream gets credit for the conversion? The downloaded buyer’s guide might have been highly influential, but if last-click attribution is in use, the email will receive credit for the conversion. This leads to erroneous decision making about which part of the media stream to optimize or where to invest further.

Marketers need a holistic method that considers everything the consumer or buyer sees, the entire media stream from the customer’s perspective, accounting for all channels and devices. With this information, marketers can accurately understand the influence of each media stream element in a conversion. Getting this view of media performance is necessary because a customer conversion is driven by all that a customer sees, not just one part of it. The great news for modern marketers is that there is a way to get this kind of information about their campaigns, and it’s called advanced or data-driven attribution.

Note

Advanced attribution helps marketers understand how every element of the media stream seen by a customer contributes to or influences conversion.

Advanced attribution is an approach for properly and precisely allocating fractional conversion credit across the entire media stream that customers are exposed to on their buying journeys. Marketers can understand how their content and media choices are helping produce conversions, and the extent to which each component in the media stream contributes to producing conversions. This level of attribution is something that marketers have longed wished for, but never thought possible. Advanced attribution is no longer in the realm of science fiction or wishful thinking: it’s here!

By using advanced attribution, marketers can know which components of their campaigns generated conversions, and even how long before the conversion event those components influenced the conversion. This information makes it much easier to distinguish between media elements that created awareness, which ones promoted interest, and which ones were most influential in the conversion. By using advanced attribution, marketers gain tremendous clarity on what’s going on with their campaigns. This information allows them to make smarter decisions about which elements of the media stream are most effective at the various stages in the buying cycle, enabling more effective use of them to get better response and conversion rates.

The capabilities of advanced attribution allow marketing to answer a series of questions that once instilled fear:

*   How does each marketing channel fractionally or incrementally contribute to the results marketing produces?
*   If marketing gets an increased budget for campaigns or promotions, where is the best place to spend it?
*   If marketing gets a decrease in budget for campaigns or promotion, where will cuts least affect the outcomes?
*   How do differences in the key campaign variables—creative, devices, audiences, and tactics—produce different outcomes?
*   How do these key campaign variables interact or work together to produce the outcomes?
*   What is the optimal blend of tactics and media channels to achieve the best possible business result?

For the marketing team using advanced attribution, when their executives want to know how much the group needs to spend to generate a needed amount of revenue, the answer is no longer an educated guess.

So far, the discussion of advanced attribution has assumed that everything marketing is doing is in the digital realm. In practical application, marketing campaigns frequently blend digital and traditional (offline) media to generate conversions. For example, a retailer might exploit several digital channels in a campaign or promotion, in addition to sending coupons through the mail, which are tracked by the retailer when customers redeem them. The coupon redemption data is added to what is also known about how those customers were influenced by the digital media to which they were exposed. In this way, the retailer gets a complete picture of how the channels worked to influence customers. The scope of advanced attribution isn’t limited to just digital media, but can include traditional media, too, as in this example. It can provide a very holistic view of everything that influences conversion.

The competitive implications of advanced attribution are enormous. For markets where the key players rely heavily on digital media, advanced attribution has the ability to tilt the playing field in favor of those who use it. Those who don’t use advanced attribution are caught in an ongoing effort of straining to see incremental improvements in media performance, because they’re making media decisions that don’t always work. Those marketers that do use advanced attribution have clarity about conversion credit, allowing them to make smarter media choices with proven results. Advanced attribution provides a new window of understanding about where conversion credit truly belongs. It puts a powerful lever in the hands of marketing for generating sales lift from every channel in use.

The three types of analytics presented in this chapter are currently the frontier of marketing analytics, but this won’t be the case for long. All of these solutions are in current use, sometimes in combination. The increased use of big data, predictive analytics, and advanced attribution by marketers is moving awareness of their benefits up, while costs and ease of use are getting better. Already these approaches are well within reach of medium and even small companies. They are no longer the exclusive domain of large, well-funded enterprise marketing teams. The advantages from using these—and most innovative solutions and approaches—comes with being agile and deploying them quickly. In other words, the longer it takes to exploit them, the more mainstream these approaches become, and the less differentiation they provide.

Today’s frontier is tomorrow’s civilized territory. So it is with big data, predictive analytics, and advanced attribution: there will come a day when “everyone’s doing it.” Marketers looking to manage their performance to higher levels and transform marketing into a true revenue center should always be scanning the horizon, looking for the next innovation to reveal itself.

Footnotes [1](#A978-1-4842-0259-3_10_Chapter.html#Fn1_source)

See [http://siliconangle.com/blog/2012/06/15/geoffrey-moore-discusses-big-data/](http://siliconangle.com/blog/2012/06/15/geoffrey-moore-discusses-big-data/) .

[2](#A978-1-4842-0259-3_10_Chapter.html#Fn2_source)

Douglas Laney, “3D Management: Controlling Data Volume, Velocity and Variety,” February 6, 2001, META Group. [http://​blogs.​gartner.​com/​doug-laney/​files/​2012/​01/​ad949-3D-Data-Management-Controlling-Data-Volume-Velocity-and-Variety.​pdf](http://blogs.gartner.com/doug-laney/files/2012/01/ad949-3D-Data-Management-Controlling-Data-Volume-Velocity-and-Variety.pdf).

[3](#A978-1-4842-0259-3_10_Chapter.html#Fn3_source)

See [http://​www.​internetlivestat​s.​com/​twitter-statistics/​](http://www.internetlivestats.com/twitter-statistics/).

[4](#A978-1-4842-0259-3_10_Chapter.html#Fn4_source)

Mick Hollison, “Love or Hate It, Why Predictive Analytics Is the Next Big Thing,” Inc. Magazine, September 17, 2014\. [http://​www.​inc.​com/​mick-hollison/​love-or-hate-it-why-predictive-analytics-is-the-next-big-thing.​html](http://www.inc.com/mick-hollison/love-or-hate-it-why-predictive-analytics-is-the-next-big-thing.html).

[5](#A978-1-4842-0259-3_10_Chapter.html#Fn5_source)

Louis Gudema, “How Predictive Analytics for B2B Sales and Marketing Can Offer Huge Returns,” Econsultancy blog, February 19, 2014\. [https://​econsultancy.​com/​blog/​64364-how-predictive-analytics-for-b2b-sales-and-marketing-can-offer-huge-returns/​](https://econsultancy.com/blog/64364-how-predictive-analytics-for-b2b-sales-and-marketing-can-offer-huge-returns/).

Index A Attitude toward marketing analytics B Business intelligence (BI) C Cavalier approach Chief financial officer (CFO) Chief marketing officer (CMO) Click-through rate (CTR) Cohort analysis Customer intelligence (CI) Customer lifetime value (CLV) Customer relationship management (CRM) D, E, F Data driven adopting and committing analytics process challenges lack of direction CMO compelling vision definition marketing analytics initiative lack of enthusiasm company crisis culture and intellectualagreement fabric of marketing insurance policy lack of freedom lack of support confirmation bias conviction disconfirmation bias effects of, bias forms metrics phenomenon pseudo-analytics rationalization bias reasoning Decision making A/B testing case study video CFO conversion rate CRM and marketing automation systems data-driven culture landing pages pipeline data video marketing G, H Google Analytics content digital analytics software HTTP data request traffic visitors I, J, K, L Internal client satisfaction M Marketing analytics advanced attribution attitude toward marketing analytics big data action plan advantage behavioral analysis communications definition information transparency insightful mathematical modern consumer drops personalization predictive modeling research techniques sentiment analysis structured data technical proficiency unstructured data variety velocity volume brand recognition budgeting process capabilities challenges channel optimization CMO content credibility culture customer understanding data usage decision making See also (Decision making) description facts/analytics data finance department flying blind improvement indispensable marketing organization internal view of marketing key characteristics of macro perspective maturity metric modern marketers operational approach organizations performance predictive analytics See (Predictive analytics) predictive intelligence primary components process pseudo-analytics process busy-ness decision making leadership scenarios marketing team metrics pursuit objectives revenue center revenue impact and expenses spectrum state of analytics vs. company size marketing budgets response categories technology CRM solutions degree of integration demand metric marketing automation predictive analytics technology landscape tools operations and logistics strategic tracking metrics over time types of valid indicators validity Marketing dashboards accurate data alignment analytics process application programming interfaces (APIs) cavalier approach collaboration data analysis development and deployment direct integration evolutionary process executive-level ideal selection of metrics key metrics performance management tool sales and marketing sharing spreadsheets tools Marketing director Marketing measurements Marketing team Metrics analytics process advantages of benchmark data customer satisfaction executions goal of modern marketing organization pay-per-click website B2B sales organizations campaign impressions categories of CFO CMO digital advertising effectiveness efficiency email marketing guidelines improvement actions marketing analytics process marketing department measurements objectives social media Website N Net Promoter Score (NPS) O Operations and logistics marketing analytics Google Analytics SEM SEO social media Organizational readiness call-to-action buttons catalyst culture characteristics corporate objectives CRM and web analytics data inventory/assessment healthy culture marketing analytics budget outside consulting services/expertise staffing systems training and education marketing analytics critical skills analytical ability communications inventory/assessment technical ability understand ability NPS data P, Q Pay-per-click (PPC) Predictive analytics cloud-based services CRM definition direct mail/telephone campaigns prospects and customers trial-and-error approach Productivity measures R Return on investment (ROI) brand equity cut expenses data access challenge data-oriented cultures degree of precision innovation and efficiency marketers marketing analytics marketing department, measurements marketing organizations measurement challenges complexity expand getting started integrate revenue transactions technical ignorance metrics profitability reporting trade show S Scorecards Search engine marketing (SEM) Search engine optimization (SEO) Strategic marketing analytics advanced attribution BI tools CI tools CLV analysis cohort analysis data-driven attribution description predictive analytics Structured Query Language (SQL) T, U, V, W, X, Y, Z Teradata Data-Driven Marketing Survey