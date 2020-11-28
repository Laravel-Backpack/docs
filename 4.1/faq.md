# Frequently Asked Questions

---


<a name="licensing"></a>
## Licensing

<a name="no-license-needed-on-localhost"></a>
### Do I need a license to test Backpack?

You don't need a license code AT ALL, if you're just installing Backpack on localhost. Just go ahead and install Backpack, you'll have zero limitations until you go to production.


<a name="licese-for-stagind-domain"></a>
### Do I need a license to put a project on a testing domain?

Yes, you do need a license code. But not a _separate_ license code. If you've already purchased a license code, you should use the same code for both your staging and production instances - even if the domains are different.

If you're interested in a license code to test your project online, _before_ you purchase a Backpack license - take note that we don't do that. We do not issue licenses for testing an already built app on a staging domain. If you've reached a point where you put your project on a staging server, that should mean Backpack has already helped you or your company save dozens or hundreds of hours of work - so that qualifies as commercial use. We recommend you purchase a license code - you can use the same license code on both your staging and production domain.

An alternative to be able to test a project online, without paying anything, would be to put your project online and bear the annoying notification bubbles. If you don't mind the notification bubbles, you can get a general idea of what's working and what's not. It's not ideal, but it's free.


<a name="backpack-license-for-open-source-projects"></a>
### Do I need a license to create an open-source project?

No, you do not. But there's a huge catch. 

Because Backpack requires a license code to work in production, all users of _your open-source project_ will require a Backpack license code for their apps to work in production. So yes, your project will be open-source, but developers _will_ need to pay for a Backpack license if their project is commercial, or apply for a free non-commercial license on our website. This makes it a bit inconvenient to use Backpack for open-source work, we know that. 

To rephrase and clarify - _you_ don't need a license code to develop an open-source project that uses Backpack. But _your users_ will. And no, if you purchase a Backpack license, your open-source users are not covered by your license, only you are. Even if you do have a Backpack license code, that is for you alone, and you should never _ever_ make that code public, or share it with anybody outside your company. 

We plan to address this inconvenience in Backpack v5, by having a "Backpack Lite" package under MIT License, with limited features. Then you could easily build things on top of that, instead of the main Backpack package. But until then, I'm afraid the problem remains - and the only solution is to clearly state in your README file that your package is open-source, but one of your dependencies requires payment for commercial use. Feel free to email hello@tabacitu.ro with details about your project, for more information.



<a name="how-do-i-use-a-backpack-license-key"></a>
### How do I use the Backpack license key I purchased/received? 

There are two places where you can put your Backpack license code:

- (A) inside your .ENV file, ona a new line, as `BACKPACK_LICENSE=xxx`
- (B) inside your `config/backpack/base.php`, at the bottom, you'll find `'license_code' => env('BACKPACK_LICENSE', false)` - you can replace `false` with your license code (wrapped by single quotes)

Option (A) is the recommended one, because there's no point in saving the license code within the code base (in git), and exposing it to whoever has access to the source code. The Backpack license code is only needed in production, so the best way is to add an environment variable there, in on your production server.
