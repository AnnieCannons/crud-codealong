# CRUD Codealong

### Learning Objectives

- Students should get exposure to `fs` methods,  such as `readFile`and `writeFile`.
- Students should get exposure to JSON parsing and stringifying.
- Students should achieve a basic understanding of CRUD operations

### Introduction

This codealong is for a Node CLI note-taking app that will use CRUD operations to manipulate the notes in a JSON file. The `data.json` file already exists.

**You'll** create a file to execute with Node. The following API usage examples assume a file called `index.js`, but you can name the file whatever you'd like.

The solution adheres to the following API (_not_ a REST API):

- Read all notes  with `node index.js read`.
- Read one note with `node index.js read [i]`, where `i` is the index of the note to be read. This is a pure index in the array—there **is no `id` field** in the note objects.
    - Example: `node index.js read 2`
- Create a note with `node index.js create [note title] [note content]`, where both dynamic fields must be contained in quotes—Node splits arguments on words if quotes don't separate them.
  - Example: `node index.js create "Persistence" "Persistence is the ability to store data past the lifetime of an application's run."`
- Update a note with `node index.js update [i] [note title] [note content]`. Note that any update needs to include both a title and the content, since overwriting the whole object is a simpler API.
- Delete a note with `node index.js delete [i]`.

### About This Codealong

#### Repo Usage

This codealong should likely be coded by the instructor with help from the group.

Right now, we have:

- Solution code to work towards.
- The data to work with in `data.json`.
- A backup of that data in `backup-data.json`, in case you accidentally overwrite the data. (If you do, be _sure_ to copy over to `data.json`, so that you retain a backup for when you accidentally overwrite it a second time.)

#### Solution API

The goal of this codealong is to get 

### Solutions

Here are some immediate solutions for quick reference.

#### Quick Solution

This solution ha a lot of nesting and may be challenging to parse.

``` javascript
const fs = require("fs");

const action = process.argv[2];

if (action === "read") {
  const id = process.argv[3];
  if (id === undefined) {
    fs.readFile("./data.json", "utf8", (err, data) => {
      const notes = JSON.parse(data);
      console.log("Here are all your notes:\n\n");
      for (let i = 0; i < notes.length; i++) {
        console.log(notes[i].title + "\n");
        console.log(notes[i].text + "\n");
      }
    });
  } else {
    fs.readFile("./data.json", "utf8", (err, data) => {
      const notes = JSON.parse(data);
      console.log("Here is that note:\n\n");
      for (let i = 0; i < notes.length; i++) {
        if (i === id) {
          console.log(notes[i].title + "\n");
          console.log(notes[i].text + "\n");
        }
      }
    });
  }
} else if (action === "create") {
  const title = process.argv[3];
  const text = process.argv[4];
  const newNote = { title: title, text: text };
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    notes.push(newNote);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
} else if (action === "delete") {
  const id = Number(process.argv[3]);
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    // Deletes 1 item starting at index `id`.
    // Research the splice array method for how this works!
    notes.splice(id, 1);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Successfully deleted note.");
    });
  });
} else if (action === "update") {
  const id = Number(process.argv[3]);
  const title = process.argv[4];
  const text = process.argv[5];
  const replacingNote = { title: title, text: text };
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    // Deletes 1 item starting at index `id`, then inserts the replacing note.
    // Research the splice array method for how this works!
    notes.splice(id, 1, replacingNote);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
} else {
  console.log('Valid actions are "create", "read", "update", and "delete".');
}
```

#### Refactored Solution

This solution uses functions to separate the handling of arguments from the actual CRUD implementation. This is the recommended solution, as it's readable by the most people, as well as how back end code is usually organized.

``` javascript
const fs = require("fs");

const action = process.argv[2];

function printAllNotes() {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    console.log("Here are all your notes:\n\n");
    for (let i = 0; i < notes.length; i++) {
      console.log(notes[i].title + "\n");
      console.log(notes[i].text + "\n");
    }
  });
}

function printNote(id) {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    console.log("Here is that note:\n\n");
    for (let i = 0; i < notes.length; i++) {
      if (i === id) {
        console.log(notes[i].title + "\n");
        console.log(notes[i].text + "\n");
      }
    }
  });
}

function saveNote(newNote) {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    notes.push(newNote);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
}

function deleteNote(id) {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    // Deletes 1 item starting at index `id`.
    // Research the splice array method for how this works!
    notes.splice(id, 1);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Successfully deleted note.");
    });
  });
}

function updateNote(id, replacingNote) {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    // Deletes 1 item starting at index `id`, then inserts the replacing note.
    // Research the splice array method for how this works!
    notes.splice(id, 1, replacingNote);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
}

if (action === "read") {
  const id = process.argv[3];
  if (id === undefined) {
    printAllNotes();
  } else {
    printNote(Number(id));
  }
} else if (action === "create") {
  const title = process.argv[3];
  const text = process.argv[4];
  const newNote = { title: title, text: text };
  saveNote(newNote);
} else if (action === "delete") {
  const id = Number(process.argv[3]);
  deleteNote(id);
} else if (action === "update") {
  const id = Number(process.argv[3]);
  const title = process.argv[4];
  const text = process.argv[5];
  const replacingNote = { title: title, text: text };
  updateNote(id, replacingNote);
} else {
  console.log('Valid actions are "create", "read", "update", and "delete".');
}
```

#### Advanced Syntax Solution

This version uses more advanced JS syntax.

``` javascript
const fs = require("fs");

const action = process.argv[2];

const printAllNotes = () => {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    console.log("Here are all your notes:\n\n");
    notes.forEach((note) => {
      console.log(note.title + "\n");
      console.log(note.text + "\n");
    });
  });
};

const printNote = (id) => {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    console.log("Here is that note:\n\n");
    notes.forEach((note, i) => {
      if (i === id) {
        console.log(note.title + "\n");
        console.log(note.text + "\n");
      }
    });
  });
};

const saveNote = (newNote) => {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    notes.push(newNote);
    const jsonVersion = JSON.stringify(notes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
};

const deleteNote = (id) => {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    const updatedNotes = notes.filter((note, i) => i !== id);
    const jsonVersion = JSON.stringify(updatedNotes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Successfully deleted note.");
    });
  });
};

const updateNote = (id, replacingNote) => {
  fs.readFile("./data.json", "utf8", (err, data) => {
    const notes = JSON.parse(data);
    const updatedNotes = notes.map((note, i) => {
      return i === id ? replacingNote : note;
    });

    const jsonVersion = JSON.stringify(updatedNotes, null, 2);
    fs.writeFile("./data.json", jsonVersion, "utf8", (err) => {
      console.log("Note successfully written to the file!");
    });
  });
};

switch (action) {
  case "read": {
    const id = process.argv[3];
    if (id === undefined) {
      printAllNotes();
    } else {
      printNote(Number(id));
    }
  }

    break;

  case "create": {
    const title = process.argv[3];
    const text = process.argv[4];
    const newNote = { title, text };
    saveNote(newNote);
  }

    break;

  case "delete": {
    const id = Number(process.argv[3]);
    deleteNote(id);
  }

    break;

  case "update": {
    const id = Number(process.argv[3]);
    const title = process.argv[4];
    const text = process.argv[5];
    const replacingNote = { title, text };
    updateNote(id, replacingNote);
  }

    break;

  default:
    console.log('Valid actions are "create", "read", "update", and "delete".');
}
```
