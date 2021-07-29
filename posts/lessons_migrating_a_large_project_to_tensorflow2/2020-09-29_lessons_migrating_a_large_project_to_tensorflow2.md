
> medium-to-markdown@0.0.3 convert
> node index.js "https://medium.com/@david010/lessons-migrating-a-large-project-to-tensorflow-2-27174292aa37"

Lessons Migrating a Large Project to TensorFlow 2
=================================================

[![David Rose](https://miro.medium.com/fit/c/56/56/1*qt83NUQLLkTSKMQ-iuMfwg.jpeg)](https://medium.com/@david010?source=post_page-----27174292aa37--------------------------------)[

David Rose

](https://medium.com/@david010?source=post_page-----27174292aa37--------------------------------)[

Sep 29, 2020·5 min read

](https://medium.com/@david010/lessons-migrating-a-large-project-to-tensorflow-2-27174292aa37?source=post_page-----27174292aa37--------------------------------)

Background on the Project
=========================

As part of the work on modernizing the content and product recommender system that we have been putting together over the years at my company, a large piece of the process involved bringing up the code to work with the TensorFlow 2.x framework. The last significant work on the recommender pipeline was in 2018 at which the TensorFlow framework was still on 1.x and the newer framework was not to be announced until the following year. But upgrading is not as simple of changing the version number. There are many incompatible changes with the API that have to be considered when making changes to a system that may have already been built with an older framework in mind.

Overall, this was both a welcome change while also creating a lot of issues of compatibility with many production systems that would have to be re-written from the ground up to take advantage of the new capabilities, though there are many temporary compatibility layers that have been used as a temporary placeholder. What may be a simple process for a researcher or a student learning to build with TensorFlow, the production systems we have here at Zeta are much more complex and have to work in concert with many other systems without issue.

Background on TensorFlow
========================

The issues go back to the way that TensorFlow was designed to perform the computations and how it was not wholly consistent with the way most people use Python. In practice Python behaves as an interpreted language (though technically it is quasi-compiled in the background during execution) which enables an easy style of debugging where you can jump in at any point to observe and modify behavior and work on potential issues.

TensorFlow on the other hand prefers to build up what they refer to as static graphs, where the values of said graphs can not be seen until the built-out graph structure has been loaded into a live TensorFlow session and the run() method is called. This creates problems down the road when trying to debug your code, as the timing of the operations of standard Python operations and the TensorFlow operations may not line up. You also can not easily move data to TensorFlow and back without multiple sessions that you have to start up and close throughout the program.

The Fix
=======

Enter eager execution. This concept attempted to reconcile the TensorFlow execution with traditional Python operations, where the ‘tensors’ and their operations were computed in real-time rather than building up a graph and then executing it all at the end. Below you can see an example of each of the two methods:

TensorFlow 1.x
--------------

<img alt="" class="fd em ei ke w" src="https://miro.medium.com/max/2216/1\*21ztOHDvV2TnVPI7YdbMPg.png" width="1108" height="744" srcSet="https://miro.medium.com/max/552/1\*21ztOHDvV2TnVPI7YdbMPg.png 276w, https://miro.medium.com/max/1104/1\*21ztOHDvV2TnVPI7YdbMPg.png 552w, https://miro.medium.com/max/1280/1\*21ztOHDvV2TnVPI7YdbMPg.png 640w, https://miro.medium.com/max/1400/1\*21ztOHDvV2TnVPI7YdbMPg.png 700w" sizes="700px" role="presentation"/>

In this output you can see that it does not know the answer yet

TensorFlow 2.x
--------------

<img alt="" class="fd em ei ke w" src="https://miro.medium.com/max/2152/1\*JKXKiytUX3XMJskDL4kadw.png" width="1076" height="744" srcSet="https://miro.medium.com/max/552/1\*JKXKiytUX3XMJskDL4kadw.png 276w, https://miro.medium.com/max/1104/1\*JKXKiytUX3XMJskDL4kadw.png 552w, https://miro.medium.com/max/1280/1\*JKXKiytUX3XMJskDL4kadw.png 640w, https://miro.medium.com/max/1400/1\*JKXKiytUX3XMJskDL4kadw.png 700w" sizes="700px" role="presentation"/>

Now with TF2 it knows the value is 10 when you print

In the first example you can see how just trying to print out the result of `2 * 5` can only tell you that it is a multiplication operation and the data type: `Tensor("mul:0", shape=(), dtype=int32)`, while in the bottom example it can compute the values when you print: `tf.Tensor(10, shape=(), dtype=int32)`.

To get the final result of a TensorFlow 1.x operation you can create a session and run the operation from that scope:

<img alt="" class="fd em ei ke w" src="https://miro.medium.com/max/2016/1\*8aqxz88ljWw5e7Zqtbfbfw.png" width="1008" height="894" srcSet="https://miro.medium.com/max/552/1\*8aqxz88ljWw5e7Zqtbfbfw.png 276w, https://miro.medium.com/max/1104/1\*8aqxz88ljWw5e7Zqtbfbfw.png 552w, https://miro.medium.com/max/1280/1\*8aqxz88ljWw5e7Zqtbfbfw.png 640w, https://miro.medium.com/max/1400/1\*8aqxz88ljWw5e7Zqtbfbfw.png 700w" sizes="700px" role="presentation"/>

To get the final result of a TensorFlow 1.x operation you can create a session and run the operation from that scope:

Other Changes
=============

There are many more upgrades and changes in the ways of using TensorFlow that won’t be gone over here, but the ones that had an impact on this particular project were:

*   **SavedModel format:** due to our recommender system running custom TensorFlow models built using non-standard layers, we encountered multiple issues when trying to save and load these models during and after training. And the TensorFlow automated conversion script can not consistently convert between them due to the lack of explicit formats files are saved in.
*   **Elimination of explicit sessions:** In TF1.x the API relies a lot upon specific practices for managing scopes of variables and graphs, which can get confusing when moving to TF2.x because we no longer explicitly create sessions and graphs. This led to issues with the TensorFlow getting confused when trying work with and convert between the multiple models we used during training: FM, BPR-FM, and the recommender predictor itself. This created issues where we encountered collisions in naming and variable access.

Conclusions
===========

Another important value in this project was learning the codebase itself and becoming more familiar with the entire pipeline of various Python libraries and frameworks that work together to provide a recommendation. All of the developers from the initial project have moved on and there was no one left knowledgeable about this system. Given this, it is good to have spent the time on trying to drill down into all the various intricacies of Python and TensorFlow, which helped to get a strong low-level idea of how everything works together.

In regards to the effort spent to achieve a working version of this system running the TensorFlow 2.x framework, I think if this was to be done again it would be much more straightforward to build up a new model based on TensorFlow 2.x rather than spent the effort digging down in to the low-level details of TensorFlow just to make everything compatible with the new API. Google tried to do a strong pivot in methods with this upgrade and while it may be a simple process for researchers or hobbyists, it can quickly become extremely complicated when working with the production systems we have built up over time here.

In all, I think it is important to continue to upgrade and implement more TensorFlow 2.x features throughout the pipeline and begin to abstract away a lot of the more technical boilerplate code that TensorFlow 1.x became infamous for. A data science team should be able to focus on where they can provide the most value for the company for their time, and the more that we are able to abstract away a lot of the low-level programming the more time we can focus on improving the models and researching new methods and techniques.
