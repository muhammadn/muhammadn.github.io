---
layout: default
title:  "Fat Models, Skinny Controller vs Separation of concerns, Part 1"
date:   2017-03-17 02:01:00 +0800
---
I started with Rails 3.2.13 and during this days, the rails community had recommended making the Rails Controller skinny (with only request code) and making the model fat which includes the business logic.

In Rails 4.1, "concerns" was introduced to separate out the business logic from controller or model and concerns helps you to build application based on the single responsibility principle. I did not pay attention to concerns until recently where i had a lot of business logic in my model.

So i had been doing a lot of refactoring of the code for a job listing site.

An example is the following code without concerns :
```
class JobsController < ApplicationController

  def index
    if params[:query].present? && params[:location].present?
      @jobs = Job.search(params[:query], fields: [ { title: :word_start }, { state: :word_start }],
                                           where: { state: params[:location], published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:query], fields: [ { title: :word_start }, { state: :word_start }],
                                           where: { state: params[:location], published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
    elsif params[:query].blank? && params[:location].present?
      @jobs = Job.search(params[:location], where: { published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:location], where: { published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
    elsif params[:query].present? && params[:location].blank?
      @jobs = Job.search(params[:query], where: { published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:query], where: { published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
    else
     @jobs = Job.where(published: true).where("published_date >= ?", 1.month.ago).page(params[:page]).per(7)
      @jobs_sponsored = Job.where(published: true).where("published_date >= ?", 1.month.ago).where("jobs.listing_type = ? OR jobs.listing_type = ?", 2, 3).order("RANDOM()").limit(2)
    end

  end
end
```
Bad innit? Code in the controller?

After re-factoring, i have the business logic code in a concern (ie. `controller/concerns/jobs_query.rb`) rather than the controller like below:
```
module JobsQuery
  extend ActiveSupport::Concern

   def jobs_with_job_name_and_location
      @jobs = Job.search(params[:query], fields: [ { title: :word_start }, { state: :word_start }],
                                           where: { state: params[:location], published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:query], fields: [ { title: :word_start }, { state: :word_start }],
                                           where: { state: params[:location], published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
   end

    def jobs_with_location
      @jobs = Job.search(params[:location], where: { published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:location], where: { published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
    end

    def jobs_with_job_name
      @jobs = Job.search(params[:query], where: { published: true, published_date: {gte: 1.month.ago} }, page: params[:page], per_page: 7)
      @jobs_sponsored = Job.search(params[:query], where: { published: true, listing_type: [2,3], published_date: {gte: 1.month.ago} }, limit: 2, page: params[:page], per_page: 7)
    end

    def all_other_jobs
      @jobs = Job.where(published: true).where("published_date >= ?", 1.month.ago).page(params[:page]).per(7)
      @jobs_sponsored = Job.where(published: true).where("published_date >= ?", 1.month.ago).where("jobs.listing_type = ? OR jobs.listing_type = ?", 2, 3).order("RANDOM()").limit(2)
    end
end

```

Do note `include JobsQuery` which Rails will autoload based on the concerns filename in the path based on Ruby On Rails convention.

```
class JobsController < ApplicationController
  include JobsQuery

  def index

    if params[:query].present? && params[:location].present?
      jobs_with_job_name_and_location
    elsif params[:query].blank? && params[:location].present?
      jobs_with_location
    elsif params[:query].present? && params[:location].blank?
      jobs_with_job_name
    else
      all_other_jobs
    end

  end
end
```

Looks short and sweet, innit?
