# Week 1 AI System Observation Sheet

## A1 - Interact with YouTube System

### 1. What is recommended on your home page right now? Can you explain why?
* **Reference Evidence:** `Figure 1: Home page` (Refer to `evidence/` folder)
* **Observation:** Right now, my home page recommends TikTok remix music (like DJ Astaga Bercanda), local entertainment videos (Ikhmal Nour, Zizan Razak, Upin & Ipin), and gaming content. This happens because YouTube tracks my past watch history, subscribed channels (such as Soloz, Mobile Legends: Bang Bang, and Ricis Official), and videos I frequently click on.

### 2. Search for something unusual - does the recommendation feed change?
* **Reference Evidence:** `Figure 2: Unusual search` and `Figure 3: Home page after unusual search` (Refer to `evidence/` folder)
* **Observation:** I searched for an unusual topic that I never watch, which is "how to build a house underwater", and watched one of the videos for about 5 minutes. When I refreshed the home page, the main video thumbnails did not change to underwater building videos at all - they still showed my usual music mixes, football highlights (Manchester City vs PSG), and podcasts. However, a small change happened on the top category bar, where a new "Survival skills" topic button suddenly appeared.

### 3. Can you observe differences across content types, or identify factors that might cause recommendations to differ across users? (Observation vs Hypothesis)
* **Observation:** We noticed that music videos get grouped automatically into playlists (My Mix), while regular videos and sports highlights are recommended as single videos. Also, my home feed looks completely different from my groupmates' feeds.
* **Hypothesis:** We assume the algorithm gives more weight to long-term watch history than a single 5-minute search session, and it also uses factors like our location (Malaysia), age group, and device type to customize the feed for different users.

### 4. Can you find any content that seems inappropriate or harmful being recommended?
* **Observation:** There is no extreme or illegal content on the home page, but the system does recommend sensational or drama-focused videos with clickbait titles (for example, podcast episodes like "POV Of A Penyondol" or over-the-top prank/challenge thumbnails) because they attract more clicks.

### 5. How transparent is the system about WHY it recommends something?
* **Observation:** It is not transparent at all. Looking at the home page, YouTube does not explain why a random football match or podcast suddenly appeared on my feed. It only shows basic labels like "#ON TRENDING" or "Mix", without telling me how my data was used to pick those videos.

---

## A2 - Complete the Observation Sheet

* **System name:** YouTube Recommendation System
* **Group members:**
  1. Amir Asyraaf Bin Zainal Abidin (A24AI0016)
  2. Nazmi Bin Sidek (A24AI0069)
  3. Amina Asyiffa Binti Aspiyah Mahyus (A23AI0015)
  4. Siti Noor Balqis Binti Yuslimi (A24AI0090)

### 1. What problem does this system solve? Who benefits?
* **Problem:** Too many videos are uploaded to YouTube every day, so users cannot manually search for everything they might want to watch.
* **Beneficiaries:** Viewers get instant video suggestions without searching, content creators get views from new audiences, and YouTube keeps users watching longer so they see more ads.

### 2. Which AI stack layer is most visible to users?
* **Layer:** Application
* **Explanation:** Users only see and interact with the website interface, such as video thumbnails, the search bar, topic tabs at the top, and subscription lists. The AI recommendation models and backend databases running behind the scenes are completely hidden from the user.

### 3. Quality dimensions (at least 3, with measurement method)
* **Dimension 1 - Personalisation Relevance:** Does the system suggest videos that match the user's actual interests?
  * **Measure:** Count how many videos in the top 10 home page recommendations match the user's main topics or get clicked by the user.
* **Dimension 2 - Adaptability (Responsiveness):** How quickly does the home feed adjust when a user explores a new topic?
  * **Measure:** Search and watch an unusual topic for 5 minutes, refresh the home page, and count how many related videos or topic tags appear.
* **Dimension 3 - Fairness (Creator Exposure):** Does the system give small creators a fair chance compared to big channels?
  * **Measure:** Check the first 10 recommended videos on the home page and compare the number of small channels under 10k views/subscribers against famous channels with hundreds of thousands or millions of views.

### 4. What did the system do WELL? Give a specific example.
The system is very good at remembering our main interests without letting one random search ruin the whole feed. For example, after searching "how to build a house underwater" (Figure 2) and watching a video for 5 minutes, the system did not flood the home page with underwater videos. Instead, it kept our favorite music mixes on the main screen while smartly adding a small "Survival skills" topic button on the top bar (Figure 3) in case we wanted to explore more.

### 5. What did it do POORLY? Give a specific example.
The system gets stuck recommending the exact same popular content and heavily favors big channels. For example, comparing our home feed before (Figure 1) and after the search (Figure 3), the exact same DJ Astaga Bercanda thumbnail stayed in the top-left spot, and almost all recommended videos came from huge channels with hundreds of thousands or millions of views (like beIN SPORTS Asia with 776K views and Zizan Razak with 18M views), giving zero visibility to smaller creators.

### 6. Responsible AI risks (at least 2. Who is harmed and how?)
* **Risk 1 - Fairness (Popularity Bias):** The algorithm mostly pushes viral videos with high view counts. Small or new content creators are harmed because their videos rarely get recommended on the home feed.
* **Risk 2 - Filter Bubbles (Over-Personalisation):** The system keeps repeating the same type of entertainment and music mixes. Users are harmed because they get trapped in a repetitive loop and miss out on diverse or educational topics, even after spending 5 minutes watching an engineering video.
* **Risk 3 - Lack of Transparency & Clickbait (Safety/Well-being):** The system promotes dramatic or clickbait titles without explaining why. Users (especially students) are harmed by wasting time on sensational content and addictive scrolling.

### 7. If you could fix ONE thing, what and why?
I would add a "Study Mode" toggle on the homepage. This feature would temporarily prioritize educational videos related to my current search and reduce entertainment recommendations, such as music, gaming, and podcasts. This would help users focus on learning new topics without permanently changing their usual recommendations or having to dislike videos they normally enjoy.

---

## A3 - Group Sharing

### Key Findings from Our YouTube Recommendation System Analysis:
1. **Finding 1 (Quality Dimension - Personalisation vs. Adaptability):**
   Our test showed that YouTube protects long-term user habits over short single sessions. Before the test, the home page showed our regular interests like TikTok remix music (DJ Astaga Bercanda), local entertainment (Ikhmal Nour, Zizan Razak, Upin & Ipin), and gaming content (Mobile Legends: Bang Bang) based on past watch history and subscriptions (Figure 1). After searching for "how to build a house underwater" (Figure 2) and watching a video for 5 minutes, the main home page thumbnails did not switch to underwater building videos at all, but kept showing music mixes, football highlights (Manchester City vs PSG), and podcasts (Figure 3). The system only adapted slightly by adding a new "Survival skills" topic button on the top category bar (Figure 3).
2. **Finding 2 (Responsible AI Risk - Fairness, Filter Bubbles, and Lack of Transparency):**
   Comparing Figure 1 and Figure 3, the exact same DJ Astaga Bercanda thumbnail stayed in the top-left spot, and almost every recommended video came from huge channels with hundreds of thousands or millions of views, such as beIN SPORTS Asia (776K views) and Zizan Razak (18M views). This creates a popularity bias that harms small creators under 10k views by giving them zero visibility, while trapping users in a repetitive filter bubble of sensational or clickbait content (like "POV Of A Penyondol") with only basic labels like "#ON TRENDING" or "Mix" and no explanation of why those videos were picked.
3. **Finding 3 (Proposed Improvement - "Study Mode" Toggle):**
   Users cannot simply click "dislike" on their favorite music or gaming videos without ruining their normal entertainment feed. Adding a "Study Mode" toggle on the homepage would temporarily prioritize educational videos related to the current search while reducing music, gaming, and podcast recommendations.