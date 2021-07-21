# StreamNative 文档贡献指南

Welcome to [StreamNative documentation](https://docs.streamnative.io/platform/latest/overview)! 

## What you can contribute

You can make contributions to any of the following items.

- Add missing contents
  
- Fix incorrect or inaccurate contents
  
- Fix typos or format (including space, indentation, punctuation, etc.)

- Resolve [doc issues](https://github.com/streamnative/snp-cn/issues?q=is%3Aopen+is%3Aissue+label%3Adoc%2Fissue).

- Review doc [pull requests](https://github.com/streamnative/snp-cn/pulls).

Taking a look at the following general information can help you get started quickly.

## Learn doc architectures

Before making a contribution, it is recommended that you read [StreamNative Platform structure](https://github.com/streamnative/snp-cn/blob/master/sn-platform-structure.md) to learn doc architectures.

## Contribution workflow

The `Fork` button is disabled for [snp-cn](https://github.com/streamnative/doc-platform) repository, so you cannot fork this repository.

### Step 1: Clone repository

1. Visit https://github.com/streamnative/doc-platform.
2. Clone the repository.

    ```bash
    mkdir -p $working_dir
    cd $working_dir
    git clone https://github.com/streamnative/doc-platform.git
    ```

### Step 2: Keep your branch in sync

Get your local master branch up to date.

```bash
cd $working_dir/doc-platform
git fetch origin
git rebase origin/master
```

### Step 3: Branch

Branch from master. The following steps take `my-branch` as an example.

```bash
git checkout -b my-branch
```

### Step 4: Edit the code

You can now edit the code on the `my-branch` branch.

### Step 5: Commit

Commit your changes.

```bash
git add <filename> 
git commit -m "$add a comment"
```

Likely, you'll go back and edit-commit in a few cycles. 

The following commands might be helpful for you.

```bash
git add <filename> (add one file)
git add -A (add all changes, including new/delete/modified files)
git add -a -m "$add a comment" (add and commit modified and deleted files)
git add -u (add modified and deleted files, not include new files)
git add . (add new and modified files, not including deleted files)
```

### Step 6: Push

When your commit is ready for review, push your branch to the origin repository.

```bash
git push origin my-branch
```

### Step 7: Create a pull request

1. Visit https://github.com/streamnative/doc-platform.
2. Click the **Compare & pull request** button next to your `my-branch` branch.

### Step 8: Get a code review

Once you open your pull request, assign it to at least two reviewers. Those reviewers will conduct a thorough code review, looking for correctness, bugs, opportunities for improvement, documentation and comments, and style.  

When you make any change in this branch, commit changes to the same branch. 
