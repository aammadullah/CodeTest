1. inside the index() method of BookingController you should not take user id as its a security risk people can access all the user's if its a incremental id. we should use auth()->user()->id if this request if behind the auth otherwise it should be a uuid atlest.
Also I see __authenticatedUser which didn't exist in laravel as far as I know so we should use $request->user() instead and directly using env() function inside controller is bad as its not allowed in laravel any more and laravel recommends to use config instead.

some of the functions inside BookingRepository is very huge for example getAll which needs to be splitted into several functions. Also we should split it into classes like email or sms notfications into a single class notification while customer methods we should put into Customer or user class

there is some code repetition in functions like sendChangedLangNotification and sendChangedLangNotification which needs to be inside a common function and these both functions should use it.

at several other places as well there are code repeatition like below
if ($this->isNeedToSendPush($user->id)) {
            $users_array = array($user);
            $this->sendPushNotificationToSpecificUsers($users_array, $job->id, $data, $msg_text, $this->isNeedToDelayPush($user->id));
 }
 is present inside sendExpiredNotification and acceptJobWithId which is not a good thing. it must be at one place inside a separate function so that later if we need to modify it then we have to change it at one place only


if you look into the below code

     if (isset($requestdata['from']) && $requestdata['from'] != "") {
                    $allJobs->where('jobs.due', '>=', $requestdata["from"])
                        ->where('jobs.status', 'pending')
                        ->where('jobs.ignore_expired', 0)
                        ->where('jobs.due', '>=', Carbon::now());
    }
    if (isset($requestdata['to']) && $requestdata['to'] != "") {
        $to = $requestdata["to"] . " 23:59:00";
        $allJobs->where('jobs.due', '<=', $to)
            ->where('jobs.status', 'pending')
            ->where('jobs.ignore_expired', 0)
            ->where('jobs.due', '>=', Carbon::now());
    }
you clearly see that whole query is repeated, I just refactered it. 

There are lots of other refactering as well but it needs time to understand the logic and then a person can properly refactor it. so far I have did very few as within this little timeframe seen these. There are alot of if else which needs time to understand and then refactor them. it also needs to be splitted into their separate classes as well.


