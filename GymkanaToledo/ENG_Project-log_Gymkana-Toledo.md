# Project log ***Gymkana Toledo***

## Summary
This project is a small and simple web application that uses HTML, JS and CSS that aims to allow a *Gymkana* to take place in the city of Toledo. This was an "express" project, created in a short amount of time (an aproximate amount of 16MH).

## Context
This project's idea was actually a product of my friend Kaelamoran, who was part-taking in an internship as a high school teacher at the time. The class she was working in was going to carry out an excursion to the historical spanish city of Toledo. She had announced that she was going to organize a *Gymkana* style activity for the class, in order to make the visit more fun for the students, giving them an excuse to explore the city at their own pace while carrying out a fun game. This *Gymkana* aimed to guide the students (separated into groups) towards different important buildings in the city using quizzes and puzzles. Once located the building or structure the quizz was referencing, the students had to take a picture of the location with themselves in it in order to prove they had reached the right location.

About a month before the event took place, she was informed that she was not going to be allowed to go to the trip, meaning it would be impossible for her to control the event on site. Apart from this, there was also the issue that it would be difficult to control such a group of people at once, without accidentally giving clues or making a group look for her, thus penalizing their response time. She then came up with the idea of asking me to create a small application to ease the *Gymkana* and allow it to be controlled in a semi-automatic manner, something that became even more important, considering that she wasn't going to be on site during the time it was taking place.

After talking about it, and discovering that *GitHub* allowed users to host small websites, I had the idea of creating a small web application to allow this activity to take place within the boundaries she set. The plan was to create a series of screens that would allow students to read the questions, upload a picture of the location, and allow a teacher to remotely validate the image.

## Project development
This project had a small planning phase with my friend Kaela, who explained her expectations. From this short pase, we identified the following screens:

* A screen for teachers and students to log in
* A screen for students to read the quizzes and upload answers
* A screen for teachers to validate answers
* A screen for a leaderboard showing the student's group classification

Shortly after this conversation, I started working on a crude prototype for this web application. This phase was very slow as I was working with technology that was new to me, in particular, *GitHub's* website publishing. I was also going through a very big transition in my work-life, so I couldn't spend as much time on this project as I would have liked. This situation only got worse when Kaela was informed that she was not going to be able to go to Toledo the day the *Gymkana* was taking place. She sent me the quizzes, clues and solutions during this time, so I could include them in the database whenever I had one.

I eneded up doing a "programming marathon", finishing most of the project in a single night about a weekend before the event took place. The final result is what we see here. There were multiple aspects that had to be changed in order to speed up the completion time of the project, specially in regards to accounts and security, due to the time limitation and the lack of time to explore other safer storage solutions.

I ended up deciding on using *Google Firebase* realtime-database, as it was faster in comparison to the latest bersion of the *Firebase* database, although also not as safe. When it came down to security, I ended up deciding to do the following:

* Instead of allowing the users to create an account, I created a series of predefined accounts for each group and another account for the teachers invigillating the event.
* The database, actually stored as a JSON object, would be accessible via the HTTP REST service calls offered by *Firebase*.
* Most of the security would be handled by the frontend, saving a user token in the browser's *session storage*.

When a user trues to access a screen, the frontend checks if the user has a token and asks them to log in if they don't. Once logged in, the token is saved in the *session storage* so the user can freely move from screen to screen. Student tokens grant access to the student-accessible screens and teacher tokens grant access to the teacher-accessible screens. The only publicly accessible screens would be the frontpage and the leaderboard (as well as the log in page, of course).

All screens were created within the specifications and, considering that the database was quite vulnerable as it was easily accessible through REST without many more firewalls, it was decided to save the images using *b64*, as to not be directly visible by just looking at the, and I promised to delete the images less than 24 hours after the completion of the event as these are considered to be personal data under the GPDR of the EU.

## Conceptual overview of the project
In this section, we discuss the conceptual target behind the different screens. This overview defines the motives and reasons behind the existance of each screen, drawing an abstraction from more technical aspects of said screens, which we will see later on in the following section. We'll go step by step through the screens in the order the user is expected to see them.

### Frontpage
The frontpage has a pretty straightforward idea. This is usually the very first page a user sees when accessing a website for the first time. In our case, we are interested in giving a simple interface that acts like a hub to allow the user to easily navigate to the relevant pages of our page application.

This page, thus, only contains a simple greeting and a series of buttons to take the user to the three main pages the web has:

* **Gymkana:** the most important page for the students, so that they can participate in the *gymkana*.
* **Leaderboard:** leaderboard page to allow the competing teams to check their advantage over one another-
* **Validate:** a slightly further apart button (to signify a difference) is the button the teacher is interested in. It leads to the screen that allows teachers to validate the students answers.

### Login page
This page is not "directly" accessible form the front page, but it will definitely be the first page that users will usually see after the front page. When a user tries to navigate to a secure screen (**Gymkana** of **Validate**), the app checks if the user has an active session. If not, this page shows up, asking the user to log in. This is a very simple page that allows the user to identify themselves to the aplication via username and password.This page will automatically redirect the user to **Gymkana** or **Validate** depending on their profile (student ot teacher accordingly).

This screen's design has been kept minimal, as simple as possible with just a textbox for the username, another for the password and a button to carry out the login action.

### Gymkana page
This is the most important page the application has to offer, used by the students to part-take in the event. After checking that the user has logged in, the page will use the retrieved data from the user to query the database for the puzzle or quizz the user is currently on. The text, images or both (if part of the puzzle) are shown to the user. An area intended to be used by the users to upload a picture is also included for them to upload their answer.

Once the answer is uploaded, they can submit it for validation. The user is then prompted to refresh their page from time to time to update it and know if their answer was correct or not. This way, the users can move on from puzzle to puzzle during the *Gymkana* event, submiting answers and refreshing to see the results.

### Validation page
This is the second most important page of the application, almoast in par with the **Gymkana** screen. The only reason for its "lesser importance" is the fact that less users would use it (one, or two maximum). On this screen, once the user has logged in and is determined to be a teacher, a list of the student's last submissions is shown to the user, as wekk as an indicator of the group they belong to and the answer to the puzzle they are trying to answer. Next to each image, the teacher can find two buttons to validate the answer, positively or negatively. This will automatically open access to the next puzzle to the students, if they were correct, or show them an extra clue if they were wrong.

Just like the **Gymnkana** page, it requires the teacher to refresh the screen from time to time, in order two see if there are any new answers.

### Leaderboard page
This page displays a table that represents the leaderboard of the groups part-taking in the *Gymkana*. The target of this leaderboard is to add a competitive touch to the event in hopes of encoraging users to try their best to come on top. It's a pretty simple screen  with the leaderboard and, as the user type intended to use this screen was the students, a button to go back to the **Gymkana** page. The leaderboard displays the data retrieved from the leaderboard and will not update real time, only when refreshed or looked at after accessing another page first.

Following Kaela's notes, the main discriminator between groups was obviously the amount of correct answers they had. If two teams reached a draw, the discriminating factor would be the time taken to solve the puzzles.

## Technical overview of the project
The project has most of its components directly in the root folder. These are, the HTML pages, that represent the views' "skeleton". These views have the main structure of the interface the users will see on screen as well as the controls (i.e. buttons) that they will be interacting with.

The directory called CSS contains the cascading style sheets (*CSS*) that gives the web application its look and feel. This directory is mostly kept in order to be consistent with other web applications as, due to the simplicity of the app, there is a single CSS file. A bigger web application would quite possibly require a larger amount, which would ol be found inthis directory. This is also related to the directory called **img**, which contains the images used for the background of the application both in desktop and in mobile devices.

Finally, we can find the JS directory, that includes the "muscle" of the application, meaning the functionality. The functionality found in these JavaScript files are the automatic checks done when accessing the pages (or refreshing them, for the matter) which include both checking if the user has logged in and/or if there is new content in the database. It also includes the operations performed when a user clicks a button.

Originally, plans had been made to create an automatic *trigger* to refresh the page's content, without requiring the user to do anything else. This, however, required some time to be used to come up with an effective way of doing it, which I didn't have at the time, as we already explained earlier in the document. This trigger was changed to being the refreshing of the page carried out by the user, which (unfortunately) required the user to manually refresh the page from time to time.

More information about the individual JS methods can be found reading the code directly, as they have been documented using the *javadoc* standard. Except for the ID token and URL of the database, these methods have been left otherwise intact.

Finally, we will mention a couple of files found in the root directory:

* **_config.yml:** an automatically generated file used by GitHub to configure the webpage. No changes were made to this file, leaving the default configuration.

* **gymkanatoledo-default-rtdb-export.json:** a JSON file extracted from the Firebase database after the *Gymkana*. It's included in the project to show the database's structure to further comprehend the code. The JSON file includes the puzzles, solutions, clues, etc. Only the answers uploaded by the students have been wiped, as they are considered personal data according to the EU's GDPR.

## Project conclusion
This application's life already had a short expectancy, as it was intended to be used only for a day. Unfortunately, the lead teacher said the application was not working; even though both Kaela and myself checked it was working as intended and that multiple users were already uploading images. We determined that the "error" was user related, which subsequently caused the applocations already short life to be shortened even further.

This projects target, however, was reached, as it was successfully developed within the set parameters, it was successfully handed in to Kaela, who checked it worked, and I was able to fulfil my personal objective of creating a small web application from scratch.

This project was motly intended as a learning experience and, as such, will be recorded in this record of projects.

## Lessons learned
I was able to apply theoretical knowledge about webapp development and design to a practical scenario. I was able to prectice HTML, CSS and JavaScript, specially interested in these last two, as I wanted to lern more about them and their possible uses.

I was also able to give my first steps in *Google Firebase*, which makes me add the need to use more time in investigating and experimenting with technologies that are mew to me to this list of lessons learned, so I can avoid future complications. Due to this mismanagement of time, I was forced to ditch a much more secure and complex security structure I had initially planned on using so make up for the lack of time. With more time and, specially, more experimentation, I could have used a more modern and secure version of *Firebase* instead of using a more "home made" solution to slightly improve security.

Finally, I will mention that this has given me the idea to create a phone application capable of creating *Gymkana* type events and to part-take in them by the use of QR codes.
