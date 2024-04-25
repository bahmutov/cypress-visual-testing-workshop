## ☀️ CI Workflow

### 📚 You will learn

- `cy.imageDiff` custom command
- Update gold images on CI workflows

---

## Custom image diff command

Using branch `c2` as the starting point

```
$ git checkout c2
$ npm install
```

I have added a custom Cypress command `cy.imageDiff`

+++

## The goal

Use the new `cy.imageDiff` command to finish the test.

```ts
// cypress/e2e/login.cy.ts
it('captures the login page', () => {
  // confirm the login form is visible
  // run the custom command "cy.imageDiff"
  // to compare the current page to the gold image
  // Tip: find the custom command TS definition in cypress/support/index.d.ts
  // and the implementation in cypress/support/commands.ts
})
```

**Tip:** try changing CSS in `src/components/SubmitButton.css` to check.

+++

## The solution

```ts
it('captures the login page', () => {
  cy.get('.login_wrapper').should('be.visible')
  cy.imageDiff('login-page')
})
```

There could **3** image diff results.

+++

## Result 1: A new image

![A new image](./img/new-image.png)

+++

## Result 2: A matching image

![A matching image](./img/match.png)

+++

## Result 3: An image difference

![Image difference](./img/no-match.png)

+++

## Assertions

As a rule of 👍:

- a functional assertion first
- followed by a visual diff command

---

## GitHub Actions Workflows

Let's review changed images on CI

```
$ git checkout c4
$ npm install
```

+++

## Make changed screenshot a new gold 🖼️

```js
// cypress.config.cjs

// if we work on a PR we want to update the Gold images
// so that the user reviews the changes
if (result.match === false && config.env.updateGoldImages) {
  console.log('Updating gold image %s', goldPath)
  fs.copyFileSync(screenshotPath, goldPath)
  result.match = true
  result.reason = 'Updated gold image'
}
```

+++

## Workflow

Set the Cypress env flag `updateGoldImages=true`

```yml
# .github/workflows/ci.yml
- name: E2E 📦
  uses: cypress-io/github-action@v6
  with:
    start: npm start
    wait-on: 'http://127.0.0.1:3000'
    # tell our image diff utility that even if the screenshot
    # differs from the gold image, we want to update the gold image
    # so that the user reviews the change in the PR
```

**Tip:** see how to pass user `env` values https://on.cypress.io/environment-variables

+++

## Solution

```yml
- name: E2E 📦
  uses: cypress-io/github-action@v6
  with:
    start: npm start
    wait-on: 'http://127.0.0.1:3000'
    # tell our image diff utility that even if the screenshot
    # differs from the gold image, we want to update the gold image
    # so that the user reviews the change in the PR
    env: updateGoldImages=true
```

+++

## Workflow in action

- make a new branch based on `c4`, like `c4-demo`
- push the branch to GitHub to see the gold images
- make a new branch and change some CSS
- push the code and open a PR to `c4-demo`
- see the changed gold images in the PR

+++

## New gold images committed by CI

![New images](./img/two-files.png)

+++

## Before and after images

![Two images](./img/side-by-side.png)

+++

## Swipe viewer

![Swipe viewer](./img/swipe.png)

---

## Even better CI workflows

In branch `c5` we have two `env` options:

- `failOnMissingGoldImage=true` _requires_ a gold image
- `updateGoldImages=true` overwrites the gold images

Get the code:

```
$ git checkout c5
$ npm install
```

+++

## Two workflows

- the `main` branch is strict: must have gold images, no diffs allowed
  - file `.github/workflows/main.yml`
- other branches allow overwriting gold images with screenshots
  - file `.github/workflows/ci.yml`

+++

## See it in action

- you need to add 3 image diffs to the Login spec
- where would you place the image diffs?
- create a new branch `c5-demo` and push it to CI
  - it should create gold images
- create another branch and change some CSS
  - push to GitHub and open a PR to `c5-demo`
  - did CI catch the visual regression?

---

## 🏁 Conclusions

- `cy.imageDiff` custom command
- CI can commit changed images on PR
- GitHub PR review shows image differences clearly

➡️ Pick the [next section](https://github.com/bahmutov/cypress-visual-testing-workshop#contents)
