# composite_action_dockertag
composite action to create dockertag from branchname using SC naming convention

# Status
By now only sets a unverifed Dockertag for Feature-Branch  

Composite Run Steps Not Support  
Don't support setting conditionals right now.

Shell must be a valid built-in (bash, sh, cmd, powershell, pwsh)
So for Python3 you need a container action see: https://github.com/RalfNellesCap/sc-build-push

# Next Try
Regex and Condition with Bash-Shell  

      - name: Check Tag
        id: check-tag
        run: |
          if [[ ${{ github.event.ref }} =~ ^refs/tags/v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
              echo ::set-output name=match::true
          fi
      - name: Build
        if: steps.check-tag.outputs.match == 'true'
        run: |
          echo "Tag is a match"

# Use
      - name: Get Dockertags for branch
        id: tag
        uses: RalfNellesCap/composite_action_dockertag@v1.0.12
        with:
          AVCHECK_VERSION: ${{ env.AVCHECK_VERSION }}
          GIT_SHA: ${{ env.GITHUB_SHA }}
          JIRA_TicketID: ${{ env.JIRA_TicketID }}
          BRANCH_PREFIX: ${{ env.BRANCH_PREFIX }}
      - name: return values action
        run: |
          echo "Dockertag:${{ steps.tag.outputs.docker-tag }}"
          echo "SHA_tag=${{ steps.tag.outputs.docker-sha }}"
      - name: Set Env Vars for Dockertags
        run: |
          echo DOCKER_TAG_POSTFIX=${{ steps.tag.outputs.docker-tag }} >> $GITHUB_ENV
          echo DOCKER_TAG_SHA=${{ steps.tag.outputs.docker-sha }} >> $GITHUB_ENV
      - name: Echo DOCKER_TAG_POSTFIX
