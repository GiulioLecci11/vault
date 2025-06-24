Prompt engineering is the art of communicating with a generative AI model.

The prompt engineering pipeline for GitHub Copilot (built on OpenAI models since those models we use have been trained to complete code files on GitHub.)
The document completion problem the LLM solves is about code, and GitHub Copilot’s task is all about completing code. But the two are very different.

-Most files committed to main are finished. For one, they usually compile. Most of the time the user is typing, the code does not compile because of incompletions that will be fixed before a commit is pushed.

-Writing code means jumping around. In particular, people’s edits often require them to jump up in the document and make a change there, for example, adding a parameter to a function. Strictly speaking, if Codex suggests using a function that has not been imported yet, no matter how much sense it might make, that’s a mistake.

Step 1) Gathering (cogliere) context: GitHub Copilot lives in the context of an IDE such as Visual Studio Code (VS Code), and it can use whatever it can get the IDE to tell it (for example the Language we're writing in or the names of directories, the files opened until that moment ecc..) but only if the IDE is quick about it though

Step 2) Snippeting: It’s important to be selective about what code to include from other files, so we cut files into (hopefully) natural, overlapping snippets that are no longer than 60 lines. Of course, we don’t want to actually include all overlapping snippets—that’s why we score them and take only the best. In this case, the “score” is meant to reflect relevance. To determine a snippet’s score, we use the Jaccard similarity, a stat that can be used to gauge the similarity or diversity of sample sets.

Step 3) Dressing them up: Now we have some context we’d like to pass on to the model. But how? Codex and other models don’t offer an API where you can add other files, or where you can specify the document’s language and filename for that matter. They complete one single document. As mentioned above, you’ll need to inject your context into that document in a natural way.
If comments work as a delivery system for tiny nuggets of information, like path (something like # filepath: foo/bar.py)  or Language (like #!/usr/bin/python), we can also make them work as delivery systems for the chunky deep dives that are 60 lines of related code.
The main problem is that on git, there are so much comments that represent things like deleted features.

Step 4) Prioritization: so far we have gathered so much information but in the vast majority of cases (around 95%), we have to make the tough choice of what we can or cannot include.
We make that choice by thinking of the items we might include as “wishes.” Each time we uncover a piece of context, like a commented out snippet from an open tab, we make a wish. Wishes come with some priority attached, for example, the shebang lines have rather low priorities. Snippets with a low similarity score are barely higher. In contrast, the lines directly above the cursor have maximum priority. Wishes also come with a desired position in the document. The shebang line needs to be the very first item, while the text directly above the cursor comes last—it should directly precede the LLM’s completion.
The fastest way of selecting which wishes to fill and which ones to discard is by sorting that wishlist by priority. Then, we can keep deleting the lowest priority wishes until what remains fits in the context window. We then sort again by the intended order in the document and paste everything together.
#AI 
[[LLM generic]]