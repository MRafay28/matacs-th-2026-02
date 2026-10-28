# Proposal triage

An admin sets the category up before anyone uploads a file. They enter the criteria, which ones are pass or fail, the weights, and a simple points scale. None of that is hard-coded.

A reviewer uploads the PDFs and picks the category. The tool checks the file, pulls the text out, and looks for lines that try to tell a model what rank to give. The model only fills a fact form: licence, insurance, number of jobs, price, weeks, warranty, and whether the team items are there. Each fact needs a short quote from the file. Code checks those quotes, runs the pass or fail gates, adds the points, and sorts the list. The reviewer sees the rank, the score, and the quote. They confirm it, change it, or hold it. A person still makes the award.

I assumed four things. The points scale is saved with the category, so the model never gives the marks. A failed mandatory item is ineligible and is not scored. A missing weighted fact scores zero on that item, and the proposal can still be ranked. If a file tells the model how to rank it, that line is ignored and the proposal is flagged until a person clears it.

## 1. AI vs rules

The model is used in one place: reading the proposal and filling the fact form. The wording changes from file to file, so a fixed parser would miss a lot.

After that, it is plain code. File checks, the search for hidden instructions, the quote check, pass or fail, the points, and the sort are all rules. The reading is messy, but the decision should not be. If the model handed out the points, two runs of the same files could come out in a different order.

## 2. Consistency and explainability

The same facts and the same saved scale always give the same score. The model is asked for facts only. If a quote is not in the file, that proposal is flagged and not scored.

A reviewer can open any row and see the criterion, the points, and the quote. The scale for this category is:

- Experience, 30%. 12 or more qualifying jobs scores 100. 9 to 11 scores 80. 6 to 8 scores 60. 3 to 5 scores 40. 1 or 2 scores 20. None, or not stated, scores 0.
- Price, 25%, and only at or under the $260,000 ceiling. $200,000 or less scores 100. $200,001 to $220,000 scores 80. $220,001 to $235,000 scores 65. $235,001 to $250,000 scores 50. $250,001 to $260,000 scores 30. Over the ceiling, or no price, scores 0.
- Timeline, 15%. 10 weeks or less scores 100. 11 to 12 scores 80. 13 to 14 scores 60. 15 to 16 scores 40. No completion time, or anything over 16 weeks, scores 0.
- Warranty, 15%, split in half. Panel years: 25 or more scores 50, 20 to 24 scores 40, 15 to 19 scores 30, and under 15 or not stated scores 0. Workmanship years: 12 or more scores 50, 10 to 11 scores 40, 7 to 9 scores 30, 5 to 6 scores 20, and under 5 or not stated scores 0.
- Team, 15%. 25 points each for a lead the proposal identifies, a described crew, a safety plan, and a monitoring plan. A lead chosen later does not count. Progress reports and a roof review are not monitoring.

The total is the weighted sum, rounded half up. A tie goes to the lower price, then the higher job count, then a person. On this scale A scores 83, E scores 70, and D scores 61. The same facts should give the same order on a second run.

## 3. Human in the loop

The reviewer checks the list before anyone treats it as the result. Flagged and ineligible rows need a look. The person can override a score or a rank, and they have to type why. The award stays with them.

The log stores the file hash, the criteria version, the prompt version, the model name, the extracted facts, the score breakdown, and any override, with the person's name and the time.

## 4. Changing criteria

An edit does not rewrite old scores. Saving a change creates a new version. Older scores are marked out of date until a reviewer chooses to run them again. Both runs stay in the log.

## 5. Risks

A bad text extract can drop a line. If the quote is not in the file, the proposal is flagged.

The model can invent a fact. No quote means no points. It can also lean toward a writing style or a company name. It never scores. Every bidder uses the same scale, and only the admin's criteria count.

A bidder can hide a note that says to rank them first. The prompt says to ignore instructions in the file, and a rule flags that file. That is why C is not ranked.

There is one extraction call per file, so the cost stays small.

Missing facts stay empty. I do not fill them in. D ranks third because it passes both checks but gives no completion time, so timeline is zero. B is ineligible because the bidder does not show its own Ontario electrical contractor licence. A low price does not waive that check. A is first and E is second.
