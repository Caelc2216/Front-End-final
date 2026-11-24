# Part 1
## Why did I select this project?
I selected this project, Option 2: PvP Typing Defense, because it seems the most fun to me. I like playing games and building small games, and if I am building something I enjoy, I am more likely to stay motivated throughout the project. I also think it is a good project for practicing a lot of the React concepts we covered in class, and I am especially interested in doing more with WebSockets.

## Required Features

**Requirement**: Player login or persistent player profiles  
Players can sign in with Google. Once signed in, they have a profile page showing their avatar, match history, and stats. Players who don't want an account can play as a guest. Guests can play matches, but their results and stats are not saved and they do not appear on the leaderboards.

**Requirement**: Start screen and playable game interface  
The start screen has an animated meteor background and buttons for Quick Play, Private Match, Leaderboards, and Profile. Quick Play puts the player in a queue for their chosen difficulty, and a match starts as soon as two players are waiting in the same queue. Private Match lets one player generate a room code that a friend enters to join the same match. The game screen shows the falling meteors, the player's health and score, the opponent's health and score, the match timer, and a typing bar at the bottom.

**Requirement**: Falling meteors displaying words  
Meteors fall from the top of the screen, each displaying one word. Meteors come in different sizes based on word length, and smaller meteors with shorter words fall faster. The match is a side-by-side race: both players get the same sequence of meteors at the same time, but each player has their own copy. Destroying a meteor on your screen does not affect your opponent's screen.

**Requirement**: Correctly typing words destroys meteors  
Players type into a bar at the bottom of the screen. As they type, the game finds any meteor whose word starts with what has been typed and highlights the matching letters on that meteor. When the full word is typed, the meteor is destroyed and the typing bar clears. If the typed letters don't match any meteor, it counts as a typing error: the typing bar and the highlighted letters turn red. The input is not cleared automatically, so the player has to backspace to fix the mistake.

**Requirement**: Points awarded for destroyed meteors  
Each meteor size has a base point value, so larger meteors with longer words are worth more. Players can earn bonus points for typing a word with no mistakes or for typing it quickly. Both players can see their own score and their opponent's score during the match, and if the timer runs out, the player with the highest score wins.

**Requirement**: Health decreases when meteors reach the base  
When a meteor reaches the base, the player loses health based on the meteor's size. If the player had already typed part of the word when it hit, the damage is reduced in proportion to how many letters they typed correctly, so partial progress still counts for something. Each player can see both their own and their opponent's health throughout the match.

**Requirement**: At least two difficulty levels  
Each difficulty level has its own Quick Play queue, so players are only matched against others who chose the same difficulty. Harder difficulties have longer words, meteors that spawn more often, and less starting health.

**Requirement**: Two-player competitive matches  
Players can get into a two-player match either through Quick Play, which pairs the first two players from the same difficulty queue, or through a Private Match, which is invite-only using a room code. In a Private Match, the player who created the room is the host: they choose the difficulty and click Start Match once their opponent has joined.

**Requirement**: Synchronized match start and end  
In Quick Play, the countdown begins as soon as both players are connected. In a Private Match, it begins when the host clicks Start Match. Either way, both players see the same countdown and the match starts for both at the same moment. Matches are timed (length still to be decided). The match ends for both players at the same moment when the timer runs out, when either player's health reaches zero, or when a player disconnects. Both players are then taken to the results screen.

**Requirement**: Winner determination and results screen  
If a player's health reaches zero, the match ends immediately and the other player wins. If a player disconnects, the player who is still connected automatically wins. When the timer runs out, the player with the highest score wins. If scores are tied, the player with the most health remaining wins. If that is also tied, the player who destroyed more meteors wins. The results screen shows the winner, the match duration, and each player's final score and stats, including how many meteors of each size they destroyed and how many hit their base. From the results screen, players can request a rematch, view the leaderboards, or return to the main menu. A rematch starts only when both players click Rematch. Until then, the player who clicked first sees a "Waiting for opponent" message. If the opponent leaves the results screen instead, the Rematch button is disabled and the player is told their opponent has left.

**Requirement**: Persistent match results and leaderboard  
When a match ends, the result is saved to the logged-in players' match history and their stats are updated. Each saved match in a player's history shows their score and whether they won. There are several leaderboards: most wins, highest score in a single game, best accuracy (meteors destroyed per typing error), and most meteors destroyed in a single game. Guests can be matched against logged-in players. In that case, the match still counts for the logged-in player and is saved to their history and stats, but nothing is saved for the guest.

## Stretch Goals
These are only attempted after every required feature works.
- **Friends list**: Add friends by username and invite them directly instead of sharing a room code.
- **Sending meteors**: Destroying certain meteors sends extra meteors to your opponent.
- **Power-ups**: Special meteors that give a temporary effect when destroyed.

# Part 2
## User Workflows
**Goal**: User can join a game   
**Steps**:
  1. User logs in using Google or joins as a guest account
  2. User clicks on quick play
  3. User selects a difficulty and joins the waiting queue
  4. If there are at least 2 people in the queue for that difficulty, then the first two people in the queue get pulled into a game

**Result**: Two users are able to play against each other  
**Potential Problems**: There is only ever one person in the queue, we could display the number of people in the queue. A player loses connection, if this happens while in the queue they would need to be removed from the queue if this was during the game the opponent would automatically win

**Goal**: User wins a game  
**Steps**:  
  1. User joins a game, and starts playing
  2. Win condition happens (player disconnects, player loses all health, time runs out)
  3. Winner is decided (player that is still connected, player that still has health, or player with the higher score when time runs out)
  4. Winner is displayed on the results screen

**Result**: Both players know that the game is over and who won via a results screen that has the users stats and the name of who won
**Potential Problems**: How to know when a user is disconnected, there would need to be something in the websocket that checks on an interval to make sure the clients are still connected. The User that is still connected should see a results screen that says {Username} disconnected. You Win

**Goal**: User can add a friend  
**Steps**:  
  1. User A is logged in and not on a guest account
  2. User A clicks add a friend and is prompted to input the friend code or username
  3. User A gets the friend code from User B or finds their profile via their username
  4. User A clicks add friend
  5. User B gets a friend request and has to accept

**Result**: User A and User B are now friends and can be seen by each other in the friends list
**Potential Problems**: The request pipeline, this would be a function on the websocket where when User A clicks add friend it sends a message to User B client for the request, when User B clicks accept it sends messages to both clients updating their Friends list.

**Goal**: User A can join User B's private match  
**Steps**:  
  1. User B selects private match
  2. User B selects create private match and is taken to a room lobby that shows the room code
  3. User B gives room code to User A
  4. User A selects private match
  5. User A selects join
  6. User A enters room code and clicks join
  7. Both users now appear in the room lobby
  8. User B selects the match difficulty
  9. User B clicks Start Match

**Result**: The countdown starts for both users at the same time, and the match begins on the difficulty User B chose
**Potential Problems**: Generating random room codes and making sure that room code doesn't already exist. This could be solved by storing the room codes in some kind of hash set or dictionary so you can easily see if it already exists. The Start Match button stays disabled while User B is the only player in the lobby, so a match can't be started without an opponent.

## Acceptance Tests
- When User A sends an invite to User B, User B can see a friend request
- When User B accepts friend request from User A both users can see each other under the friends list
- When User A disconnects from game, win condition shows User B as the winner
- When User B's health goes to 0, win condition shows User A as the winner
- When a word on a meteor is correctly typed, that meteor no longer appears on screen
- When an meteor hits the players baseline, that players health is decreased
- When a logged-in player wins a match, their win count is increased by 1
- When the timer runs out, the player with the higher score is shown as the winner, even if they have less health
- User A can join User B's private match via a room code (possibly a friend invite if that gets built)
- When a user incorrectly types a character that doesn't match the word, the word flashes red and the user can continue by deleting that character

# Part 3
## Pages and Navigation
**Home**
- Purpose: The main page users see, this is where they perform navigation to all of the other parts of the app
- Displays: This page has buttons for quickplay, private match, leaderboards, and profile
- Actions: Users can click on any of the displayed buttons
- Navigation: URL, this is the main page. You can leave it by clicking any of the other buttons on the page  

**Game**
- Purpose: This is where the actual gameplay happens
- Displays: This shows the meteors with the words you need to type to destroy them, it has an input box, your health and score and your opponents health and score across the top along with the time remaining. There is a baseline near the bottom of the screen, with your profile icon in the middle
- Actions: Users can type words into the input box to match the words that appear on the meteors, once typed correctly the meteor disappears. 
- Navigation: Clicking quickplay and waiting for a match, or clicking start from a private match, the only way to leave is by closing the app or finishing the game

**Results**
- Purpose: Shows the results and stats of the match that just concluded
- Displays: The winner of the match, both players' final scores, along with your User stats, typo mistakes, and number of meteors destroyed for each meteor size
- Actions: clickable button rematch, back to main menu, and view leaderboard
- Navigation: Appears after game ends, you can leave by clicking the rematch, back to main menu, or view leaderboard buttons

**Leaderboard**
- Purpose: Shows the leaderboard for each difficulty and what stat you want to filter by
- Displays: names of avatars and ranking for the given difficulty and stat selected
- Actions: Tabbed menu at the top to filter by difficulty with a filter on the leaderboard for most wins, highest score, accuracy, or most meteors destroyed, also an exit/back to menu button
- Navigation: YOu get to it by clicking on the leaderboard button on the main page, you can leave it by clicking the back button

**Profile**
- Purpose: See your stats and edit your avatar
- Displays: Your game stats, display name, avatar icon, and recent matches with your score and whether you won
- Actions: Change avatar icon, change display name, back to main menu
- Navigation: You get to it by clicking the profile icon in the top right of the home page, you can get our of it by clicking the back button

**Login**
- Purpose: Allows users to log in
- Displays: Google sign in button or continue as guest button
- Actions: Log in via Google or continue as guest
- Navigation: You get to it by clicking the profile button when not logged into an account, you get out of it by clicking login with google and completing the steps or clicking continue as guest

**Lobby**
- Purpose: See who is in your current lobby and customize game settings
- Displays: Room code, avatar icons and display names of those that are in your lobby. Also difficulty level and start button 
- Actions: Set difficulty for the match, start match and kick players from lobby
- Navigation: You get to it by clicking create a private match or by joining a private match, you can leave it by clicking leave lobby or by starting a game

## Component Planning
**Avatar**
- Responsibility: Holds an icon or image and applies appropriate styling to make it look like an avatar along with an optional display name

**Meteor**
- Responsibility: Displays an image of a meteor styled appropriately based on the size that is passed into it, along with a word relative to the size of the meteor

**StatsCard**
- Responsibility: A sylized way to display the different stats of a match

**HeathBar**
- Responsibility: Displays the amount of health that a player has left

**QueueBanner**
- Responsibility: A visual indicator to the User that they are currently in the queue to join a match

## Interface Planning
Users with naviagte through the application by clicking buttons. Important information will stand out by being bigger on the screen, such as timer stats, or having an accent color like the health bars. Haven't thought too much about color schemes yet. The page will be responsive to different users with the background just being cut and the menus adjusting to the new screen sizes. Users will know if an action succeeded or failed based on whether the page changes or shows an error message, for example the button is disabled in a private match with message need 2 players to start a match. If an oppenent disconnects that will be displayed as part of the win logic results screen.

# Part 4
## Technical Plan
**React + TypeScript** will be used to write the pages, components and types for the application, such as the type meteor size, or difficulty

**Tailwind CSS** will be used as an easier way to style components instead of using basic CSS. It will be used on the menus and game screen to make them responsive so that they can respond well to different screen sizes

**React Router** will be used to navigate and conditionally render different pages without having to reload the whole component/browser. An example would be when a user logins to the app, or when a win condition is met. It will also be used to check and make sure that the user is authenticated before being able to view the profile page. For the game page and game results page you need to be one of the users in the game

**TanStack Query** will be used to query the database when displaying leaderboard stats and profile match history, it will also be used to update display name and avatars 

**State Management** I will be using useState to keep track of the user input in the game as well as if a meteor is active or destroyed. I will use a SocketContext to hold the information and functions that need to be called in the socket.

**Forms** and validation will be used on the room codes, display name editor, and login credentials if I create my own authentication system

**Authentication** Will be handled through Google and a custom system built and later implemented if I have extra time.

**Backend + Persistence** will be handled using Node.js and Postgres to send and receive data through TanStack. Stored data goes over REST requests using TanStack and the live game data goes over WebSockets

**WebSockets** Will be used to sync the data between users, such as time, score, and health. WebSockets will also be used for room codes, and friend invites. WebSockets will also handle the win logic and issue the result screen to show up on both client devices

## Data Planning
**Players**: This will be a table with an id, username, display name, avatar icon, and google id

**Matches**: This will have a match id, difficulty level, foreign key to player1, and a foreign key to player2, guest_name for if there is a guest, winner id, end reason, started at, ended at, and is private

**Stats**: This will have a id, and a fk to player id, a fk to match id, as well as meteors destroyed for each size, score, final health, typing errors, and meteors hit base

**Friends**: This will have an id, a fk to issuer, and a fk to addressed to, along with a status that can be invited, accepted, or declined

Players have many matches, a match has 2 stats, and a friend row links 2 players

## Backend and Authentication
**What data your frontend will request or send?**
### REST
- player username
- player login token
- player avatar icon
- player display name
- friend status
- friend issuer and addressed to
- player stats
- match result, win/loss

### WebSocket
- room codes
- quick play queue
- player login token
- lobby states
- meteor details
- health
- timer
- rematch requests

**What information needs to be saved permanently?**
- player username
- player google id
- player avatar icon
- player display name
- friend status
- friend issuer and addressed to
- player stats
- match result, win/loss


**What functionality requires authentication?**
- Profile data
- Saving stats
- Friends list
- Appearing on leaderboards

**How will the frontend communicate with the backend?**  
Stored data goes over REST using TanStack Query and the live game data goes over WebSockets 

# Part 5
## Development Order
- Game page
- WebSocket Server
- Private match
- Database
- API
- Google login
- Leaderboard
- Home page
- Profile page
- Friends
- Custom Authentication

First program checkin is on Nov 30th which is just in a week. I want to have an initial instance of the game, even if it is just single player, it would be great if I could also have the WebSocket Server started on, but because it is Thanksgiving week I'm not sure on how much I will actually get done, but it's not like we have a lot of time to work on the final

## Anticipated Challenges
**Keeping both instances in sync**

**making meteors falling smooth**

**determining which meteor the user is trying to type at**

## Priorities
I will prioritize my remaining work by what are the actual requirements of the assignment and what can I get done in the remaining amount of time. I will use AI to help me throughout so that I can make sure that I am hitting all of the required requirements for this assignment.