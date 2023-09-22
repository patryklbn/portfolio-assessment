# Workflow

This portfolio entry provides an overview of my understanding and application of workflow tools on GitHub. Specifically, it covers how I managed and executed tasks throughout their lifecycle:

* Accepting tasks from the project backlog
* Updating task information as required
* Progressing tasks on the task board
* Carrying out development on a feature branch
* Committing changes with meaningful comments
* Ensuring that work aligns with the Definition of Done (DoD)
* Creating pull requests

The specific issue tackled involved creating a local database using SQLite, implementing CRUD functionality, and ensuring its reliability through unit tests.

## Task workflow

### Accepting the task

In deciding on our workflow, our team opted to manage our tasks using a Kanban board on zube.io, which is seamlessly integrated with our group's GitHub repository. One of our team members took the initiative to import the provided issues into our Kanban board as a backlog. We collectively decided that team members could independently choose issues from the backlog. Upon selecting an issue, I opened the card, assigned myself to it, and shifted the card's status on the board to In Progress. Subsequently, I verified that I was also assigned to this [issue on GitHub](https://github.com/timh1975/UNDAC-App-Project/issues/5 "Issue 5"). 

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/15.png?raw=true" alt="Kanban board">
  <figcaption><b>Fig.1 - zube.io - Assigned task card and kanban board</b></figcaption>
</figure>

### Development

I began my development process by creating a new feature branch in our group repository. This approach ensures the separation of my work from the master branch, minimizing the potential for conflicts with my teammates' contributions. As a team, we agreed upon a naming convention: feature/feature_name-name-surname-ID-taskID. To establish the new branch, I executed the following steps:

* Opened the terminal.
* Navigated to the directory containing my cloned shared repository.
* Created and immediately checked into the new feature branch using the command:

```git checkout -b <feature/continents-patryk-gronczewski-ID-5>```



