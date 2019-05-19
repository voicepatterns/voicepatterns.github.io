Violet Conversations are created by connecting a set of elements in a Voice Script. Violet manages these elements, allows for defining them in a user-experience supportive manner, and also allows developers to configure them as needed.

Violet builds on core conversation concepts below.

## Phrases (Utterances), Intents, and Parameters (Slots)
In a conversation, a user can say one of many Phrases that map to a single Intent (action). Phrases can include a parameter from the user.

## States
Voice conversations consist of a set of requests from the user and responses back to the user. In order to support these user events and track where a user is in a voice script, Violet needs to track and manage the users' conversational state.

## Branching
Voice scripts are unique in that they need to support a lot of branching (to support the various user intents). These require support for nested case-like statements with additional support for repeating prompts as requested by the user, supporting misrecognitions, else logic, and modularization rules.

## Flow
User requests need to be handled at multiple levels in the branching heirarchy:
- for the top-level (global for app)
- that match any elements tagged as *active*
- that match the branches of the hierarchy from the most recent prompt

If a leaf element finishes processing and it does not have any children then the conversation flow moves back up the tree (we are essentially doing a depth first traversal with the user picking branches).

## Frames
Frames allow for the explicit presentation of a hierarchy to users in a voice conversation. They allow the user to say "go back".

A frame is a point in the flow that the conversation will return to when the flow below the frame finishes and has nothing more to talk about. 

Menus are most often branches + frames.

A frame could also be used to guard an area of the application from un-intentional exit. (e.g. Are you sure you want to quit, your progress will be lost).

Examples of frames:

**Menus with a frame** - Insurance Advisor does this. At the top of the app you get this question:

- A: Would you like to take the assessment quiz, or get answers to common insurance questions
- U: Take the quiz.
- A: Ok. Here's your first question....
- U & A do the quiz.
- A: Thank you for taking the quiz. Would you like to take the assessment quiz, or get answers to common insurance questions

The above is a frame, since the user returned to the earlier decision point when they finished the quiz flow.

**Menu w/o a frame** - Insurance Advisor does something like this when hearing answers to insurance questions:

- A: Would you like to take the assessment quiz, or get answers to common insurance questions?
- U: Answer some questions
- A: Do you want to hear about auto insurance, life insurance, or renter's insurance <=== This is a menu w/o a frame
- U: Auto
- A: Auto insurance is ......... blah blah blah .......  Would you like to take the assessment quiz, or get answers to common insurance questions?

Not all menus are worth returning to when lower-level conversations wrap up. These menus don't have frames

**Frame w/o a menu ** - You could probably think of one for any interaction that asks a question. That could be yes/no, pick from a list, a menu,  etc.

Yes/No:

- A: Do you want to play the game?
- U: Yes
- U & A play the game
- A: Do you want to play again?

Pick from a large list:

- A: Which football team do you want to hear statistics about?
- U: Baltimore Ravens
- A: The Baltimore Ravens scored ..... Which Football team do you want to hear statistics about?

