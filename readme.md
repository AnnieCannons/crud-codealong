# CRUD Codealong

### Learning Objectives

Students should have exposure to:

- `fs` methods,  such as `readFile`and `writeFile`.

Students should have basic understanding of:

- CRUD operations

### About This Codealong

This codealong should likely be coded by the instructor with help from the group.

Right now, we have solution code for you to work up to, as well as 

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

    break;
  }

  case "create": {
    const title = process.argv[3];
    const text = process.argv[4];
    const newNote = { title, text };
    saveNote(newNote);

    break;
  }

  case "delete": {
    const id = Number(process.argv[3]);
    deleteNote(id);

    break;
  }

  case "update": {
    const id = Number(process.argv[3]);
    const title = process.argv[4];
    const text = process.argv[5];
    const replacingNote = { title, text };
    updateNote(id, replacingNote);

    break;
  }

  default:
    console.log('Valid actions are "create", "read", "update", and "delete".');
}
```
