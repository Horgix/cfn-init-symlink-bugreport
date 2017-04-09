# Summary

This file contains the messages between my team and AWS. If by any chance
someone at Amazon ends up here and wants to take a look at it again, this is
the Case # 2001371931.

# Initial report

Wed, Dec 21, 2016 at 2:53 PM

> Hello,
>
> We noticed a potential bug in Cloudformation when using cfn-init
>
> Could you please have a look on the following report and advise?
>
> https://github.com/Horgix/cfn-init-symlink-bugreport

# Amazon

Wed, Dec 21, 2016 at 5:29 PM

> Hello,
> 
> Thank you very much for the detailed report on the issue.
> 
> I ran a couple of tests with the CloudFormation template in your GitHub repository, and I noticed the permissions on the /etc/issue file indeed changed.
> 
> I'm going to submit this to our internal service team for their feedback, I'll get back to you as soon as I get word from them.
> 
> In the meantime, if you have any other question, please don't hesitate to get back in touch and we'll be glad to assist you.
> 
> Have a nice day!
> 
> Best regards,

# Amazon again

Wed, Dec 21, 2016 at 10:36 PM

> Hello there,
> 
> My name is [redacted] and I wanted to provide you with an update from the service team regarding the target permissions on the symlink.   Currently right now, CloudFormation does not support retaining the target's permission when creating the symlink.   A feature request has been created for this use case.  Unfortunately we do not have any etas on when this would be implemented.   I would suggest keeping an eye out at https://aws.amazon.com/new/ for any new CloudFormation announcements.
> 
> If you have any other questions, please feel free to reach out to us again.
> 
> Best regards,

# Amazon still

Thu, Dec 22, 2016 at 12:29 PM

> Hello again,
> 
> I've been in contact with our service team, and there is an update on the case.
> 
> We are still investigating the bug, but so far we've managed to reproduce the issue in both Ubuntu and Amazon Linux, which suggests that there might be a problem with the CloudFormation Helper Scripts.
> 
> Seems like there was a misunderstanding with the previous correspondence, this issue is not subject of a feature request but a bug fix since permissions on a target file should not be modified when creating a symlink.
> 
> Again, thank you very much for bringing this to our attention and I'll keep you updated with any development on the case.
> 
> Have a nice day!
> 
> Best regards,

# Amazon statement

Tue, Dec 27, 2016 at 10:47 AM

> Hello again,
> 
> I have an update from our internal service team regarding the case.
> 
> Seems like many of our customers depend on the current behavior of the symlink creation with the cfn-init script, therefore there is little chance this would be changed since it wouldn't be backwards-compatible, and most likely will break many CloudFormation templates that are currently on production.
> 
> The service team is considering implementing a new way to create symlinks with cfn-init that doesn't modify the permissions in the target file, while keeping the current way in order to avoid issues with working CFN stacks. Also, we acknowledge that the documentation could be more explicit regarding the modification of the permissions in the target file while creating symlinks, the team is working on rephrasing the documentation to make sure this behavior is explained explicitly.
> 
> Unfortunately we do not have a precise time for the release of improvements or fixes as it is heavily dependent on the workload of the relevant team at the given time. For updates on the matter, I'd suggest to stay tuned to our 'What's New' [1] page or our Release Notes page [2].
> 
> Thank you for bringing this to our attention.
> 
> If you have any other question, please don't hesitate to get back in touch and we'll be glad to assist you.
> 
> Have a nice day!

# My reply

Mon, Jan 2, 2017 at 5:32 PM

> Hello [redacted],
> 
> > Seems like many of our customers depend on the current behavior of the symlink
> > creation with the cfn-init script, therefore there is little chance this would
> > be changed since it wouldn't be backwards-compatible, and most likely will
> > break many CloudFormation templates that are currently on production.
> 
> I get that some of your customers depend on the current behavior and
> that you don't want to break it for them; but isn't the
> AWSTemplateFormatVersion field in the stack description here for this
> kind of situation ?
> 
> I mean, isn't it possible to keep the current behavior for the current
> AWSTemplateFormatVersion (2010-09-09), while fixing this symlink bug in
> a new version ? So this way it will not break anything for existing
> users except if they explicitly upgrade the AWSTemplateFormatVersion
> without reading the patchnotes, and will allow new users and users
> interested in the fix to use/upgrade to the new version.
> 
> Thanks for your time,
> 
> Have a nice day!

# Amazon final answer

Mon, Jan 2, 2017 at 8:06 PM

> Hello,
> 
> Thank you for the suggestions, Our team is always working on ways to make sure existing workflows are not broken.
> 
> As mentioned by the previous engineer they are currently considering ways to add this functionality without changing current template behaviors.
> 
> Although I am unable to provide an ETA, keep an eye out for new updates and changes.
> 
> Thanks again for your time and suggestions.
> 
> Best regards,

