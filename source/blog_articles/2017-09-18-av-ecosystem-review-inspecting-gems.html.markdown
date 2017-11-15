---
title: AV EcoSystem Review Inspecting Gems
date: 2017-09-18
tags: 
author: Sam Joseph
---

![gems](../images/gems.jpg)

So my AV ecosystem reviews are getting further afield.  Here I am inspecting different gems for the purpose of completing a feature to support multiple project repositories.  What are the goals of this ongoing review? It's to focus my blogging power onto making the AV ecosystem as welcoming and easy to use as possible.  That's the flow of people from external sources to our website, to our Slack instance and to contributing to the projects we have hosted on GitHub.  This feature is a response to a request from a project maintainer, and that's a stakeholder that we might have overlooked, so I'll see what giving them focus can do.

Last Friday after spiking an interface component for dynamic form fields in rails, I found some gems that might provide a more coherent approach.  [https://github.com/ncri/nested_form_fields](https://github.com/ncri/nested_form_fields) is my current lead candidate.  243 stars suggests it has some popularity and I can see that it was updated as recently as five days ago.  There's also [https://github.com/ryanb/nested_form](https://github.com/ryanb/nested_form) with 1,769 stars, but hasn't been updated in 4 years.  Another [quick search](https://www.google.co.uk/search?q=gems+rails+nested+form) shows me there's also [https://github.com/nathanvda/cocoon](https://github.com/nathanvda/cocoon) with 2,445 stars and was updated as recently as 3 days ago.  Glad I did that search, or I might not have found this other important contender which looks like the field leader at the moment.  Let's try it out - following the README instructions on how to install ...

Using Cocoon requires the nested models that we haven't set up yet, and then has options for using Rails Formtastic, SimpleForm or Standard Rails forms.  I'm tempted to go for standard Rails forms, since I don't want to take on too much at once.  I'll start with the migration, I guess, to pull the github_url out of the project and into a separate table ...

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (761_multiple_source_repository)]$ 
→ rails g model SourceRepository url:string
      invoke  active_record
      create    db/migrate/20170918083218_create_source_repositories.rb
      create    app/models/source_repository.rb
      invoke    rspec
      create      spec/models/source_repository_spec.rb
      invoke      factory_girl
      create        spec/factories/source_repositories.rb
```

I can then edit the created model to have the setup that Cocoon requires:

```rb
class SourceRepository < ActiveRecord::Base
  belongs_to :project
end
```

and refer to it in the existing Project class:

```rb
class Project < ActiveRecord::Base
  ...
  
  has_many :source_repositories, inverse_of :project
  accepts_nested_attributes_for :source_repositories, reject_if: :all_blank, allow_destroy: true
```  

Using an online HAML to ERB converter I can get the project example forms in ERB, and adjust to our needs:

```erb
<%= form_for @project do |f| %>
  <div class="field">
    <%= f.label :name %>
    <br/>
    <%= f.text_field :name %>
  </div>
  <div class="field">
    <%= f.label :description %>
    <br/>
    <%= f.text_field :description %>
  </div>
  <h3>Source Repositories</h3>
  <div id="source_repositories">
    <%= f.fields_for :source_repositories do |source_repository| %>
      <%= render 'source_repository_fields', f: source_repository %>
    <% end %>
    <div class="links">
      <%= link_to_add_association 'add source_repository', f, :source_repositories %>
    </div>
  </div>
  <%= f.submit %>
<% end %>
```

and the partial:

```erb
<div class="nested-fields">
  <div class="field">
    <%= f.label :url %>
    <br/>
    <%= f.text_field :url %>
  </div>
  <%= link_to_remove_association "remove source repository", f %>
</div>
```

and after a few misteps back and forth to add the correct references to the model/migration:

```rb
class CreateSourceRepositories < ActiveRecord::Migration
  def change
    create_table :source_repositories do |t|
      t.string :url
      t.string :url
      t.references :project # <-- needed this

      t.timestamps null: false
    end
  end
end
```

I was rewarded with seeing the boiler plate form allowing me to both add and remove source repositories:

![](https://dl.dropbox.com/s/vy7u9shkciiljc7/Screenshot%202017-09-18%2009.53.22.png?dl=1)

Hopefully a bit of cleanup and formatting and this will look nice and have more functionality that I was able to immediately hand-roll.  I could probably have implemented dynamic removal of form fields in the same time it took me to get proof of concept on this gem.  However using this gem will hopefully make our system less suprising to new developers who might have seen this gem on other projects, and we'll potentially get the benefits of upgrades and fixes to the gem itself.

Of course, despite getting the interface working, the repository datat that I inputted was not actually saved to the database as such, but then I hadn't got that part working in my own hand-rolled version either :-)

I can see the data being passed back:

```
Started POST "/projects" for ::1 at 2017-09-18 08:56:32 +0000
Processing by ProjectsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"0/yym9qPkJcAgoxhJqfg2UIOfDEFIDda2qW2VWZCVl2AF0/fytCniCb8bJb/qy1ZV0f/CXYDc9mvGBElB51A4g==", "project"=>{"title"=>"test", "image_url"=>"", "description"=>"test", "status"=>"Active", "github_url"=>"", "source_repositories_attributes"=>{"1505724773160"=>{"url"=>"test", "_destroy"=>"false"}, "1505724775431"=>{"url"=>"test2", "_destroy"=>"false"}, "1505724776802"=>{"url"=>"test3", "_destroy"=>"false"}}, "pivotaltracker_url"=>""}, "commit"=>"Submit"}
  User Load (0.6ms)  SELECT  "users".* FROM "users" WHERE "users"."deleted_at" IS NULL AND "users"."id" = $1  ORDER BY "users"."id" ASC LIMIT 1  [["id", 3]]
  Event Load (1.5ms)  SELECT "events".* FROM "events" WHERE "events"."category" = $1  [["category", "Scrum"]]
Unpermitted parameter: source_repositories_attributes
   (0.2ms)  BEGIN
  Project Exists (2.8ms)  SELECT  1 AS one FROM "projects" WHERE ("projects"."id" IS NOT NULL) AND "projects"."slug" = 'test' LIMIT 1
  SQL (4.6ms)  INSERT INTO "projects" ("title", "description", "status", "github_url", "pivotaltracker_url", "image_url", "user_id", "slug", "created_at", "updated_at") VALUES ($1, $2, $3, $4, $5, $6, $7, $8, $9, $10) RETURNING "id"  [["title", "test"], ["description", "test"], ["status", "Active"], ["github_url", ""], ["pivotaltracker_url", ""], ["image_url", ""], ["user_id", 3], ["slug", "test-8018e213-701c-40bd-aa02-caa3025ae4ba"], ["created_at", "2017-09-18 08:56:33.028974"], ["updated_at", "2017-09-18 08:56:33.028974"]]
```

I was about to say that I'd leave fixing that till tommorrow, but I had a quick idea it was to do with strong parameters, and it was:

```rb
  def project_params
    params.require(:project).permit(:title, :description, :pitch, :created, :status, :user_id, :github_url, :pivotaltracker_url, :pivotaltracker_id, :image_url, source_repositories_attributes: [:url])
  end
```

We need to specify the nested required attributes.  We are now saving our new repo information to the database thanks to [Stack Overflow](https://stackoverflow.com/questions/15919761/rails-4-nested-attributes-unpermitted-parameters)!






