---

title: Configuring Git Bash and SSH for GitHub
date: '2016-01-10 18:40:21'
---

GitHub has excellent [documentation](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/) for adding an existing project, which can be briefly summarized as follows: 

1. [Create a new repository on Github](https://help.github.com/articles/creating-a-new-repository)
2. Initialize a local repo, add files, and commit (<code>git init</code>, <code>git add .</code>, and <code>git commit -m "Commit message"</code> respectively)
3. Add the Github repo as a remote (<code>git remote add origin [repo_url]</code>
4. Push changes to Github (<code>git push origin master</code>)

When pushing code, you will be prompted to enter your GitHub username and password. If you push code often or have a complex password/passphrase (as you probably should), this will quickly get tedious.

An alternative is to [use SSH keys](https://help.github.com/articles/working-with-ssh-key-passphrases/) and configure an authentication agent to avoid having to re-enter credentials. Git Bash can be configured to automatically run ssh-agent. Add the following into your ~/.bashrc file (or create a new one if it doesn't exist):

<pre><code>
# Note: ~/.ssh/environment should not be used, as it
#       already has a different purpose in SSH.

env=~/.ssh/agent.env

# Note: Don't bother checking SSH_AGENT_PID. It's not used
#       by SSH itself, and it might even be incorrect
#       (for example, when using agent-forwarding over SSH).

agent_is_running() {
    if [ "$SSH_AUTH_SOCK" ]; then
        # ssh-add returns:
        #   0 = agent running, has keys
        #   1 = agent running, no keys
        #   2 = agent not running
        ssh-add -l >/dev/null 2>&1 || [ $? -eq 1 ]
    else
        false
    fi
}

agent_has_keys() {
    ssh-add -l >/dev/null 2>&1
}

agent_load_env() {
    . "$env" >/dev/null
}

agent_start() {
    (umask 077; ssh-agent >"$env")
    . "$env" >/dev/null
}

if ! agent_is_running; then
    agent_load_env
fi

# if your keys are not stored in ~/.ssh/id_rsa or ~/.ssh/id_dsa, you'll need
# to paste the proper path after ssh-add
if ! agent_is_running; then
    agent_start
    ssh-add
elif ! agent_has_keys; then
    ssh-add
fi

unset env
</code></pre>

My GitHub SSH key is named github\_rsa, so I had to change the two lines in the last if-statement from <code>ssh-add</code> to <code>ssh add ~/.ssh/github\_rsa</code>.

Now, when you run Git Bash, ssh-agent will start automatically and you will be able to <code>git push</code> and <code>git pull</code> from GitHub without needing to enter any credentials.
