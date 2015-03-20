# CS 270 - Spring 2015 - QR Code Scavenger Hunt - Phase 3

In Phase 2, we set up the framework to associate Events and Locations
together.  In this phase, we will incorporate the functionality in the
Event controller to add a Location to an Event listing.

The first thing to understand is which controller methods in the Events
controller are being utilized in this process.  When we start the Rails
server and visit the URL `http://domain/events`, we get a listing of all
events.  This is created when the `index` method in the Events
controller retrieves all Event objects in the database and passes them
to the `index.html.erb` view for rendering.

When you click the "edit" link for a particular event, that triggers the
`edit` method in the controller, which simply retrieves that particular
event and passes it to the `edit.html.erb` view, which in turn yields
controler to the `_form.html.erb` view for rendering.

Notice the embedded Rails tag `<%= f.submit %>` at the bottom of the
form page.  This tag renders a "submit" button that incorporates the
changes made on the edit page.

How does it do that?  By triggering the `update` method in the Events
controller.  All the values that are entered in the edit form are passed
along to that method in the controller to be updated in the database.

Rails accomplishes this in the controller through a variable called
`params`.  This variable is a hash that contains the field names
from the edit form as keys, and their entered values as the hash values.

Recall in Phase 2 we created a checkbox for each Location to be added
and stored those locations in an array called `add_locations[]`.  This
array is also passed as a parameter.

Since we have already created the structure for the
has_and_belongs_to_many association between Events and Locations, we can
simply iterate through this array and shift each location onto the
Events.locations array inside the `respond_to` block before we call
`@event.update`:

```
Location.find(params["add_locations"]).each do |location|
  @event.locations << location
end
```

This simply uses the Location.find class method, which can take in an
array of ids, and iterates over each of them and adds that Location
object to the listing of Locations for that event.  We can verify that
this has occurred by entering the Rails console and querying the
Locations for the appropriate Event id.  (i.e., for Event 1,
`Event.first.locations`.)

Next, we need to update the "show event" page to display the
currently associated Locations for each Event.  We do this by adding
markup/embedded Ruby to the `show.html.erb` view as we did for the edit
page:

```
<p>
<strong>Locations:</strong>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Tag</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <% @event.locations.each do |location| %>
    <tr>
      <td><%= location.name %></td>
      <td><%= location.tag %></td>
    </tr>
  <% end %>
</table>
</p>
```

Obviously, the difference is we have no need to edit from this page, so
we remove the appropriate markup.
