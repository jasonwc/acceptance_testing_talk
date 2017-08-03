
# [fit] How I Learned To Love Acceptance Testing

---

## Jason Carter
### Software Engineering Team Lead
#### @jason URUG
![](mavenlink_logo.png)

---

### Extreme Programming
### Pair Programming
### Test Driven Development
### Rad Hype Videos
### <sub>[mavenlink.com/engineering](mavenlink.com/engineering)</sub>

![autoplay mute loop](mavenlink_vid.mp4)

---

# A quick story...

---

# le me
![fit left](http://www.memes.at/faces/happy_smile.jpg)

---
# Test Driven Development
![](http://i.imgur.com/acmyARH.png)

---

## ...so that means, write a __unit__ test, implement it
![fit right](http://www.memes.at/faces/questioning.jpg)

---

## __Unit tests__ gave me confidence that __my class__ worked as expected

---

## On __unit__ tests...

---

## But there was a problem...

---

![fit](http://ilkinulas.github.io/assets/integration_tests/no_integration_test.gif)

---

## So I got better at writing __integration__ tests...

---

## __Integration tests__ gave me confidence that __my classes worked together__ as I expected

---

## On __integration__ tests...

---

![fit](https://i0.wp.com/motionmill.com/wp-content/uploads/2013/03/what-the-customer-really-wanted-528x514.gif)

---

## So how do you make sure that you're building the _right_ thing, and that it works?

---

![fit](http://www.memes.at/faces/questioning.jpg)

---

# ¯\\_(ツ)_/¯

---

## Fast forward a year...

---

### First big project as a team lead
![fit](https://s-media-cache-ak0.pinimg.com/originals/be/0a/b1/be0ab19142b6bd196c8ab7f7429310d2.png)

---
## Overengineering
## Rejections galore
![](http://www.memes.at/faces/whyyy.jpg)

---

## Start with an acceptance test

---

## Acceptance testing
>Formal testing with respect to user needs, requirements, and business processes conducted to determine  whether or not a system satisfies the acceptance criteria and to enable the user, customers or other authorized entity to determine whether or not to accept the system.

-- International Software Testing Qualifications Board

---

## Acceptance Testing
>A test that must pass for the user story you're working on to be complete
-- Jason Carter

---

## Turns out this is totally part of XP

---

![fit](http://www.extremeprogramming.org/map/images/loop.gif)

---

>The name acceptance tests was changed from functional tests. This better reflects the intent, which is to guarantee that a customers requirements have been met and the system is acceptable.

-- Don Wells, extremeprogramming.org

---

## Now you may be thinking...

---

## Isn't that what __QA__ is for?
![fit left](http://www.memes.at/faces/questioning.jpg)

---

## They have __better__ things to do

---

## They're great at finding __edge cases__
![fit left](https://pbs.twimg.com/media/CFBzC9gWgAAXrZL.jpg)

---

## They're great at identifying __wonky__ behavior

---

## They should be your __last__ line of defense, __not your first__

---

![fit](http://www.kappit.com/img/pics/201601_0639_igdgf_sm.jpg)

---

## The __acceptance test__ tells you when you're __done__... immediately

---

## Pattern becomes:
- Write the __acceptance test__ to know when the feature is done
- Test drive the implementation with __unit__ and __integration specs__
- Refactor safely knowing that the __feature still works__ the way it should

---

## So how do we accomplish this in a Rails app?

---

# Selenium

---

## Usually people have _strong_ opinions on Selenium...

---
## Should be used as a last resort
![left fit](http://images.memes.com/meme/896966)

---

## That's QAs job!
![right fit](https://s-media-cache-ak0.pinimg.com/originals/76/0f/56/760f56ac6f0e99c43a7e0f9c86430308.jpg)

---

## It fails for no reason!
![left fit](https://images-gmi-pmc.edge-generalmills.com/7d823bda-b234-49d9-9d84-83b8cc5b12a5.jpg)

---

## Me
![fit](https://s-media-cache-ak0.pinimg.com/736x/cb/cc/7e/cbcc7e1f3506a3c3ea56392e43e57010--famous-memes-rage-faces.jpg)

---

## So we set out to make it palatable to _me_...

---

## ...and to _product_ and _QA_.

---

## Topics
- General principles
- Avoiding "intermittent failures"
- Vanilla
- Setup, Actions, and Assertions
- Page Object pattern
- Random musings

---

## Principles

---

## An __acceptance test__ should...

---

## Exercise your app to its __fullest__

---

## __Avoid mocking__ as much as possible

---

## Only simulate what a __user__ could do

---

## __Give you confidence__ that your app is working as it should

---

## Be the __first test__ you write in the case of a regression

---

## Avoiding "intermittent ~~failures~~ successes"

---

## Race conditions

---

## Explicit vs implicit wait

---

## Not scoping your finders

---

## Asynchronous code

---

## Mixing setup and actions

---

## Vanilla

---

```ruby
describe 'when deleting an existing Review', js: true, type: :request do
  # A bunch of lets
  context 'When the user is a review creator' do
    it 'can be deleted' do
      find('.workspace-proof-title', text: proof.title).click
      within '.workspace-proofs-table' do
        within 'tr', text: 'some proof' do
          expect(page).to have_text('In Review ( 1/2 )')
        end
      end
      within '#side-panel' do
        find('span', text: 'Delete Review').click
      end
      within '.show-mavenlink-alert' do
        expect(page).to have_selector('h2', text: 'Delete')
        expect(page).to have_selector('button', text: 'Delete')
        expect(page).to have_selector('button', text: 'Cancel')
        click_button('Delete')
      end
      within '#side-panel' do
        expect(page).to have_button('Create Review')
      end
      find('.js-panel-close-link').click
      find('.workspace-proof-title', text: 'Some Proof').click
      within '#side-panel' do
        expect(page).to have_button('Create Review')
      end
      within '.workspace-proofs-table' do
        within 'tr', text: proof.title do
          expect(page).to have_no_text('In Review ( 1/2 )')
        end
      end
    end
    end
  end
 end
 ```
---

# Pros

---

## At the very least we're avoiding intermittent failures

---

# Cons

---

## Only a dev can read this, and probably only one with context

---

## Mixes assertions and actions

---

## Tough to create a new test immediately

---

## It offends my __DRY__ sensibilities

---

## If I change the DOM, I have to update a bunch of stuff...

---

## ... and I might not know what the behavior was in the first place?

---

## But what about comments, Jason?

---

## Comments are just another thing to keep up to date

---

#### <sub>Sure comments are sometimes necessary</sub>

---

## A good method name is generally a fine comment

---

## And if you need comments, chances are your method is doing too much*

---

#### <sup>*</sup> Jason opinion

---

## So if we don't like comments, how do we make this more understandable?

---

## Setup, Actions, and Assertions

---

 ```ruby
 describe 'when deleting an existing Review', js: true, type: :request do
 	# a bunch of lets
    it 'can be deleted' do
      navigate_to_proof_tab(proof_employee, workspace)
      click_proof_title(proof)
      it_should_have_proof_with_status(proof, 'In Review ( 1/2 )')
      click_delete_review
      confirm_action 'Delete'
      it_has_create_review_option
      it_persists_the_delete(proof)
      it_should_not_have_proof_with_status(proof, 'In Review ( 1/2 )')
    end
  end
```
---

# Pros

---

## Definitely easier to read

---

## Reusable methods!

---

## Abstracted away the DOM

---

# Cons

---

## Not always easy to tell what page we're on

---

## Not a great way to show that an action leads to an assertion

---

## Not pictured is our giant function library

---

## Page Object Pattern

---

```ruby
describe 'inviting people to a workspace', js: true, type: :request do
  describe 'the invitation flow' do
    context 'when adding to consultants' do
      context 'when the account is not free or trialing' do
      	let(:project_invitation_form) { Pages::ProjectInvitations::Form.new('maven') }
        let(:inviting_user) { users(:jane) }
        let(:invited_user) { users(:alice) }
        let(:workspace) { workspaces(:jane_car_wash) }

        it 'will invite a maven to a workspace' do
          login(inviting_user)
          visit workspace_path(workspace)
          invite_a(team, invited_user)

          expect(project_invitation_form).to have_save_message_as_default_checkbox
          expect(project_invitation_form).to have_no_subject_and_message_tipsy
          expect(project_invitation_form).to have_no_upgrade_call_to_action
          expect(project_invitation_form.send_invitation).to have_sent_an_invitation
        end
      end
    end
  end
end
```

---

```ruby
module Pages
  module ProjectInvitations
    class Form
      include Capybara::DSL

      def initialize(team)
        @team = team
      end

      def has_save_message_as_default_checkbox?
        page.has_css?('#save_message_as_default')
      end

      def send_invitation
        click_button 'Send invitation'
        self
      end

      def has_sent_an_invitation?
        within('.flash-container') do
          page.has_css?('.notice', text: '1 invitation was sent for this project')
        end
      end

      def has_not_sent_an_invitation?
        within('.flash-container') do
          page.has_css?('.notice', text: 'The invited user must be a member of your account.')
        end
      end
    end
  end
end

```

---

# A couple things to note

---

```ruby
def has_sent_an_invitation?
  within('.flash-container') do
    page.has_css?('.notice', text: '1 invitation was sent for this project')
  end
end

# Selenium does some magic for us
# has_sent_invitation? -> have_sent_an_invitation
```

---
Returning `self` lets us chain things together, and use the `to` syntax

```ruby
def send_invitation
  click_button 'Send invitation'
  self
end
```

---

```ruby
describe 'inviting people to a workspace', js: true, type: :request do
  describe 'the invitation flow' do
    context 'when adding to consultants' do
      context 'when the account is not free or trialing' do
      	  # ...
          expect(project_invitation_form.send_invitation).to have_sent_an_invitation
        end
      end
    end
  end
end
```

---

# Pros

---

## Much easier to tell what page we're on!

---

## We can scope actions and assertions to pages (or subpages!)

---

## Still pretty readable! In fact, lets us see cause and effect much easier

---

## Martin Fowler said its [awesome](https://martinfowler.com/bliki/PageObject.html)!

---

# Cons

---

## I haven't found any yet...

---

# Other musings

---

```ruby
expect(project_invitation_form.send_invitation).to [
	have_sent_an_invitation,
	have_notified_some_service,
	have_done_something_else
]
```

---

## What if I want to make the same assertions on multiple pages?

---

## Shared assertions between page objects

---

## What about common tasks like logging in?

---

## Shared actions between page objects or done before setting up the page object.	

---

## 3rd party integrations?

---

## We integrate with a 3rd party via Delayed Job

---

## We can assert that Delayed Job is called, but what about that it does the right thing?

---

```ruby
def run_conceptshare_jobs_immediately
  stub(Delayed::Job).enqueue do |job|
	job.perform if job.respond_to?(:queue_name) && job.queue_name == 'conceptshare'
	true
  end
end
```

---

```ruby
context 'when adding to a workspace mapped to conceptshare' do
  it 'will create a conceptshare mapping for the participation' do
    run_conceptshare_jobs_immediately
    mock.any_instance_of(Conceptshare::AccountClient).create_user_if_necessary(invited_user)
    mock.any_instance_of(Conceptshare::AccountClient).add_user_to_account(invited_user)
    mock.any_instance_of(Conceptshare::AccountClient).add_user_to_project(invited_user, workspace, "Commentator")

    login(inviting_user)
    visit workspace_path(workspace)
    invite_a(team, invited_user)
    project_invitation_form.send_invitation
  end
end

```

---

## This gets us partway there

---

## How do I make sure the 3rd party does what I want?

---

## VCR?

---

## What if you still got the behavior wrong?

---

## Clarify with QA and product up front

---

## Have them look at your now readable acceptance tests

---

## But Jason, this all sounds well and good...

---

## How do I start?

---

![](https://media.giphy.com/media/Z0dvuZszc3r3i/giphy.gif)

---

# In Summary

---

## We're __avoiding common pitfalls__...

---

## We're avoiding __"building the wrong thing"__...

---

## It serves as __documentation__ thats readable by all stakeholders...

---

## It makes it __easier__ to do sweeping refactors and maintain behavior...

---

## ... and

---

## Me now
![fit](http://www.memes.at/faces/epic_win.jpg)

---

# Questions?
