# Photogram WRITE

In this project, we'll be working with the same old domain model:

![Domain Model](erd.png?raw=true "Domain Model")

In this project, our starting point is the [target of the last project, Photogram READ](https://photogram-read-target.herokuapp.com/). Our goal is to add the last piece: allowing users to _WRITE to_ our database, in addition to _READing from_ it.

### Here is [our new target](https://photogram-write-target.herokuapp.com/).

[https://photogram-write-target.herokuapp.com/](https://photogram-write-target.herokuapp.com/)

Note that the target is using a dark theme for its default tag-level styles, while we are using a light theme for ours; to help tell them apart.

## Setup

 1. From [your Cloud9 repositories list](https://c9.io/account/repos), set up a workspace [as usual](https://guides.firstdraft.com/starting-on-a-project-in-cloud9).
 1. Set up the project: `bin/setup`
 1. Start the web server by clicking "Run Project".
 1. Navigate to your live application preview. You should see what looks like the target of Photogram READ, but with no data in any tables yet.
 1. Run `rails dummy:reset` to pre-populate your tables with some dummy development data.
 1. As you work, remember to navigate to `/git` and **always be committing**.

> Note that if for some reason later you want to reset the database again, you need to first destroy it:
>
> ```bash
> rails db:drop
> ```
>
> and then re-create and re-populate it:
>
> ```bash
> rails db:migrate
> rails dummy:reset
> ```

## Tasks

Ultimately, your task is: make your app's behavior match the target (ignoring styling).

But, here's a broken out list of tasks:

### /delete_photo/[ID]

When a user visits e.g. `/delete_photo/42`, the row in the photos table with the ID of `42` should be deleted.

After the record has been deleted, the user should be forwarded to `/popular`.

On the photo details page, add a link to make it easy to delete photos (rather than having to type `/delete_photo/42` into the address bar).

### /new_photo_form

When a user visits `/new_photo_form`,

 - Display a form with:
    - A text input for the URL of an image.
    - A textarea for a caption.
    - A dropdown list of usernames.
    - A button to "Add photo".
 - When the form is submitted, it should send the user to `/create_photo_record`; along with the info that they typed into the form tacked on to the end of the URL as a query string.

#### Hints

##### Textareas

Textareas are one of the few form controls that do not use the `<input>` element with a `type=""` attribute to specify a variant. Instead, they get their own element: `<textarea>`:

```html
<textarea></textarea>
```

Unlike `<input>`, `<textarea>` does require a closing tag. You can still use `name=""`, `id=""`, and `placeholder=""` attributes the same way you do with `<input>`:

```html
<textarea id="caption_box" name="pic_caption" placeholder="Enter a caption for the photo..."></textarea>
```

##### Dropdowns

The HTML for a dropdown is very similar to the HTML for an unordered list. Use `<select>` instead of `<ul>` and `<option>` instead of `<li>`:

```html
<select>
  <option>Catherina</option>
  <option>Jolie</option>
  <option>Trina</option>
</select>
```

However, _usually_ we want the form to display a friendly thing (like a username) in the dropdown, but submit a more useful thing (like an ID number) into the query string (and thus the `params` hash of the next action).

To achieve this, we use the `value=""` attribute on the `<option>` elements:

```html
<select name="poster_id" id="user_dropdown">
  <option value="98">Catherina</option>
  <option value="96">Jolie</option>
  <option value="82">Trina</option>
</select>
```

And, of course, we'll usually use embedded Ruby to create all of these `<option>`s, rather than handwriting the HTML. It will usually be a `.each` very similar to when we drew multiple rows from a table on an index page:

```erb
<select id="user_dropdown" name="poster_id" >
  <% User.all.order({ :username => :asc }).each do |a_user| %>
    <option value="<%= a_user.id %>">
      <%= a_user.username %>
    </option>
  <% end %>
</select>
```

### /create_photo_record

When a user visits `/create_photo_record` along with a query string containing photo info, enter a new record in the photos table based on the info.

After the record has been saved, the user should be forwarded to `/recent`.

### /existing_photo_form/[ID]

When a user visits e.g. `/existing_photo_form/42`, look up the row in the photos table with the ID of `42` and then:

 - Display a form with:
    - A text input pre-populated with the URL of its image.
    - A textarea pre-populated with its caption.
    - A button to "Update photo".
 - When the form is submitted, it should send the user to `/update_photo_record/42`; along with the info that they typed into the form tacked on to the end of the URL as a query string.

To make it easy for a user to reach this URL, add a link to the photo details template to get there.

#### Hints

 - To pre-populate `<input>` elements, we use the `value=""` attribute:

    ```html
    <input value="https://robohash.org/eligendiprovidentsed.png?size=300x300&set=set1" type="text" name="pic_image" id="image_input" placeholder="Enter a URL for the image...">
    ```
 - To pre-populate `<textarea>` elements, make the stuff you want to show up **the content of the element**:

    ```html
    <textarea name="pic_caption" id="caption_box" placeholder="Enter a caption for the photo...">Every flight begins with a fall.</textarea>
    ```

### /update_photo_record/[ID]

When a user visits e.g. `/update_photo/42` along with a query string containing photo info, look up the row in the photos table with the ID of `42` and then update the record based on the info.

After the record has been saved, the user should be forwarded to the details page of the photo, i.e. `/photos/42`.

## The Golden Seven

The RCAVs that we built above to support Creating, Updating, and Deleting photos, together with the ones that we had already written for Reading them, look basically like the RCAVs for CRUDing to/from any other database table; and RCAVs of this pattern comprise 98% of RCAVs in real-world applications.

The standard set of seven RCAVs that we build for each database table are:

 - An RCAV to display a list of rows.
 - An RCAV to display the details of a single row.
 - An RCAV to delete a record.
 - An RCAV to display a blank form (for adding a new record).
 - An RCAV to actually create a new record.
 - An RCAV to display a pre=populated form (for editing an existing record).
 - An RCAV to actually update an old record.

We refer to these as **The Golden Seven**, because they underlie _everything_.
