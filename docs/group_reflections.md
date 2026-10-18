# Group Reflections (Group 4 - YouTube Recommendation System)

**Group Members:**
1. Amir Asyraaf Bin Zainal Abidin (A24AI0016)
2. Nazmi Bin Sidek (A24AI0069)
3. Amina Asyiffa Binti Aspiyah Mahyus (A23AI0015)
4. Siti Noor Balqis Binti Yuslimi (A24AI0090)

---

## Part A Reflection

**Question:** *After the group-sharing session, compare conversational AI systems with recommendation systems. Identify one quality dimension that differs between them and support your answer using an observation presented by another group.*

**Answer:**
Comparing our analysis of the YouTube Recommendation System with the findings presented by other groups on conversational AI systems (ChatGPT and Claude.ai), the Topic 1 quality dimension that differs the most between these two types of systems is **Accuracy (Factual Correctness) versus Personalisation Relevance and Adaptability**.

1. **How Conversational AI Systems Work (Observations from Other Groups):**
   * In conversational AI systems like ChatGPT and Claude.ai, users ask direct text questions and expect a single accurate answer. Other groups observed that when they asked a factual question (*"What is the capital of Malaysia?"*), system quality was measured by **Accuracy**, specifically whether the AI gave the factually correct answer (*"Kuala Lumpur"*) without hallucinating. When they tested out-of-scope requests (*"Can you book a flight for me?"*) or harmful prompts (*asking for instructions to access someone else's account without permission*), the systems immediately responded with clear text explanations and safety refusals.

2. **How Recommendation Systems Differ (Observations from Our YouTube Test):**
   * In contrast, a recommendation system like YouTube does not have one "factually correct" home page answer, and it never explains its decisions in text. Instead, its quality depends on balancing **Personalisation Relevance, Adaptability, and Fairness**:
   * **Personalisation vs. Adaptability:** Before our test, our home page (`Figure 1`) recommended our regular interests like TikTok remix music (*DJ Astaga Bercanda*), local entertainment (*Ikhmal Nour*, *Zizan Razak*, *Upin & Ipin*), and gaming content (*Mobile Legends: Bang Bang*) based on past watch history and subscriptions. While ChatGPT or Claude immediately switch topics on the very first prompt, when we searched for *"how to build a house underwater"* (`Figure 2`) and watched a video for 5 minutes, YouTube's main home page thumbnails did not switch to underwater building videos at all (`Figure 3`). It protected our long-term habits (still showing music mixes, *Manchester City vs PSG* football highlights, and podcasts) and only adapted slightly by adding a new **"Survival skills"** topic button on the top category bar (`Figure 3`).
   * **Fairness and Lack of Transparency:** Unlike conversational AI which communicates openly via text, YouTube operates silently at the Application layer with only basic labels like *"#ON TRENDING"* or *"Mix"* and zero explanation of why videos were picked. Comparing `Figure 1` and `Figure 3`, the exact same *DJ Astaga Bercanda* thumbnail stayed in the top-left spot, and almost every recommended video came from huge channels (*beIN SPORTS Asia* with 776K views and *Zizan Razak* with 18M views), giving zero visibility to small creators under 10k views and trapping users in a filter bubble with clickbait titles like *"POV Of A Penyondol"*. Because users cannot simply click "dislike" on their favorite music or gaming videos without ruining their normal feed, recommendation systems require user controls like our proposed **"Study Mode" toggle** on the homepage to temporarily prioritize educational videos.

---

## Part B Reflection

**Question:** *Identify which component or interaction has the most potential failure points. Explain what this suggests about where AI Engineering effort should be concentrated, referring to the quality dimensions introduced in Topic 1.*

**Answer:**
Based on our system mapping and empirical observations, the components with the highest concentration of potential failure points are the **Ranking Model** and its downstream interaction with the **Recommended Home Feed (Application UI)**. This indicates that AI Engineering effort cannot simply focus on predictive accuracy, but must be heavily concentrated on algorithmic re-weighting and UI-level explainability.

1. **Components with the Most Potential Failure Points (What Could Go Wrong?):**
   * **The Ranking Model:** This component carries the highest concentration of systemic risk annotations on our map. Because it optimizes heavily for historical engagement metrics (such as past watch time and click-through rates), it is highly prone to **popularity bias** (making it nearly impossible for smaller or newer creators to gain visibility) and **over-reliance on past behavior** (trapping users in a rigid filter bubble where they only see repetitive content).
   * **Recommended Home Feed (User Interface Interaction):** At the presentation layer, the primary failure point is a complete **lack of transparency and user control**. The interface displays recommended videos with zero explanation of *why* a specific video appears in the feed, leaving users without an intuitive way to adjust their short-term session intent without disrupting their long-term profile.

2. **Where AI Engineering Effort Should Be Concentrated:**
   * **Algorithmic Level (Ranking Model & Content Policy Filter):** Engineering effort must be concentrated on fine-tuning the Ranking Model's objective functions to actively counteract popularity bias and inject controlled diversity (exploration vs. exploitation) into the feed. Furthermore, stronger integration with the **Content Policy Filter** is required to ensure sensationalist or clickbait content is not disproportionately rewarded just because it generates high initial clicks.
   * **Application Level (Explainability & Control Layer):** Engineers need to build an explicit explanation and control layer within the User Interface. Rather than acting as a black box, the UI should inform users why specific content is being recommended (e.g., *"Recommended because you watched X"*) and provide session-level controls (such as our proposed *"Study Mode"* toggle) so users can actively steer their feed in real time.

3. **Connection to Topic 1 Quality Dimensions:**
   * **Adaptability:** Tying directly into our Part A findings, the Ranking Model's over-reliance on long-term history directly causes poor **Adaptability**. Watching a 5-minute underwater building video (`Figure 2`) failed to shift the main home page thumbnails (`Figure 3`), proving that the model is too rigid in prioritizing past habits over immediate user intent.
   * **Fairness:** The popularity bias annotations in our system map directly explain the poor **Fairness** observed during testing. The algorithm overwhelmingly promoted massive channels (such as *Zizan Razak* with 18M views and *beIN SPORTS Asia* with 776K views) while giving zero visibility to creators with under 10k views.
   * **Personalisation Relevance:** While the system excels at capturing long-term preferences, it fails at dynamic **Personalisation Relevance**. By keeping the Top 10 recommendations locked around the exact same music mixes (*DJ Astaga Bercanda*) and gaming videos (*Mobile Legends: Bang Bang*), the system restricts content discovery and fails to serve the user's evolving, present-moment interests.