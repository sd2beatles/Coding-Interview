1. Convert string to date
%Y -year
%m -month
%d -date
%H -hour
%i -minute
%s -second


Test 1)  




solution)

```sql

with active_user as(
   select Year(subscription_started) as year,
          count(*) as subscribers
          from test
          group by Year(subscription_started)),
	 merged as(
     select active_user.year,
            active_user.subscribers as sub,
            inactive_user.unsubscribers as unsub,
            lag(active_user.subscribers,1) over(order by active_user.year) as pr_sub,
            lag(inactive_user.unsubscribers,1) over(order by inactive_user.year) as pr_unsub
       from active_user
       inner join(select Year(subscription_ended) as year,
          count(*) as unsubscribers
          from test
          group by Year(subscription_ended)) inactive_user
		on active_user.year=inactive_user.year)
	select   year,
             sub as subscribers,
			 unsub as unsubscirbers,
             case when (sub-unsub)=(pr_sub-pr_unsub) then "NA"
                  when (sub-unsub)<(pr_sub-pr_unsub) then "below"
                  when (sub-unsub)>(pr_sub-pr_unsub) then "above"
                  else "NA" end as year_change
    from merged;


```
