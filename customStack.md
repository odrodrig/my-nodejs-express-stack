# Building a custom stack

Steps for creating custom stack:

1. Ensure that you have the latest stacks by running
  `appsody repo list`

2. If you do not have a repo that points to `https://github.com/kabanero-io/collections/releases/download/0.6.3/kabanero-index.yaml` then run:
  `appsody repo add incubator https://github.com/kabanero-io/collections/releases/download/0.6.3/kabanero-index.yaml`

3. Create a new stack based on the existing nodejs-express stack
  `appsody stack create my-nodejs-express-stack —copy incubator/nodejs-express`

4. Change directory into your new stack directory
  `cd my-nodejs-express-stack`

5. Make your changes to the stack to accomplish your goals.

6. From your new stack directory (`my-nodejs-express-stack`), run the following to package your stack. This will build your stack image and archive the source files for your templates. These will be stored in `~/.appsody/stacks/dev.local`

7. Verify that the previous command succeeded by running the following:
  `appsody repo list`

  You should see a new repo named `dev.local`

8. Retag stack image for your image registry

`echo DOCKER_HUB_USERNAME=xxxxx` (replace `xxxx` with your own Docker hub username)

`docker tag dev.local/appsody/my-nodejs-express-stack $DOCKER_HUB_USERNAME/my-nodejs-express-stack:0.2.10`

9. Push to docker hub

`docker login`

`docker push $DOCKER_HUB_USERNAME/my-nodejs-express-stack:0.2.10`

10. Push the new stack directory to GitHub.

   1. Go to Github and create a new empty repo

   2. Copy the repo address

   3. In your temrinal ensure that you are in the new stack directory and run the following:
   `git init`
   `git remote add origin xxxxx` (replace xxxxx with your new repo address from the previous step)
   `git add .`
   `git commit -m "initial commit"`
   `git push -u origin master`

11. In your browser refresh the page with your new repo and you should see your stack files show up. Click in the `Release` tab.

12. Click on `Draft a new release`

13. In the `Tag version` field enter `v0.2.10-custom` or something similar. Then click on the section near the bottom of the page to upload files.

14. In the window that comes up, navigate to `odrodrig@us.ibm.com/.appsody/stacks/dev.local` (instead of `odrodrig@us.ibm.com`, it should be your local username) and upload the `tar.gz` files. There should be 3 files: one for the source, and two for the templates. 

If the `.appsody` directory doesn't show up, pres `cmd + shift + .` in the file browser window to show hidden files.

15. From the stack directory in your terminal, run the following to create the repo index file (Add your own github user and repo names to the url):
`appsody stack add-to-repo my-org-repository --release-url https://github.com/myorg/my-org-repository/releases/latest/download/`

16. Navigate to the `~/.appsody/stacks/dev.local` directory and edit the new index yaml file. Change the value of the image property at the very bottom to:
`docker.io/xxxx/my-nodejs-express-stack:0.2.10` (Replace xxxx with your docker hub username)

17. In github edit the release that was just created and add the newly generated yaml file that can be found in `~/.appsody/stacks/dev.local` directory. 

18. Edit the kabanero resource by inserting the following under the `repositories` property while changing the url, name, and image properties to your own values.

```yaml
- https:
         url: https://github.com/odrodrig/my-nodejs-express-stack/releases/download/v0.2.10-custom/odrodrig-custom-index.yaml
       name: oliver-custom
       versions:
       - images:
         - id: my-nodejs-express-stack
           image: docker.io/odrodrig/my-nodejs-express-stack:0.2.10
```
