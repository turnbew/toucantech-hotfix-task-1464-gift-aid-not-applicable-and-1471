TASK DATE: 04.04.2017 - FINISHED: 04.04.2017

DIFFICULTY: EASY - TWO JOBS iN ONE 

TASK SHORT DESCRIPTION: 1464 [
								Total giving figure shouldn't calculate donations with 
								the 'gift aid not applicable' option for gift aid status
							]
							
						1471 [
							"Weekly newsletter template changes
							- Use only two text sizes, one for header (16px) and one for body (13px)
							- Align all headers to the left
							- Hide the banner at the top if no banner image has been uploaded"	
							] - RELATED TO TASK 962
						
						TEST PART AGAIN WAS PUT INTO THE CODE!!!!!
						
						
GITHUB REPOSITORY CODE: hotfix/task-1464-gift-aid-not-applicable

ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/hotfix/task-1464-gift-aid-not-applicable

HELPER LINKS  

http://test14.toucantech.com/admin-portal/fundraising/donationdetails/1076
http://test14.toucantech.com/admin-portal/members/edit/123
C:\toucantech.dev\network-site\addons\default\modules\network_settings\controllers\members.php
C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\members\form.php

HELPER LINKS END

CHANGES (1471)

	NEW FILE: 
	
		\network-site\addons\default\modules\clubs\views\email_templates\weekly_newsletters\email_banner.php

	IN FILES:
	
		\network-site\addons\default\modules\clubs\events.php

			function changed totally
			
				public function get_school_banner()
				{
					$bannerSnippet = '';
				
					$bannerImg = Settings::get('theme_custom_logo4'); //clubs email banner

					if( ! empty($bannerImg) and file_exists ( __DIR__ . "../../../../uploads/default/files/" . $bannerImg )) {
						$emailBanner = "<img src='" . site_url("/uploads/default/files/" . $bannerImg) . "' style='width: 100%; height: auto;'/>";
						$bannerSnippet = $this->ci->load->view('clubs/email_templates/weekly_newsletters/separator_section', array('emailBanner' => $emailBanner), true);
					}

					return $bannerSnippet;
				}
				

CHANGES
 
	IN FILES: 
	
		\network-site\addons\default\modules\fundraising\models\payments_m.php
		
			Inside function total_donations_for_user
			
				CHANGED CODE I: 

					FROM:  	$query="SELECT id, payment_amount, payment_date_received  
					To:		 $query="SELECT id, payment_amount, payment_date_received, giftaid_status 

				CHANGED CODE II:
				
					FROM:	
						$day = date('d',strtotime($donation['payment_date_received']));
						$month = date('m',strtotime($donation['payment_date_received']));
						$year = date('Y',strtotime($donation['payment_date_received']));
						if($this->declarations_m->check_for_specific_donation($id, $donation['id'])) $donation['payment_amount'] *= 1.25;
						elseif($this->declarations_m->check_for_date($id,$day,$month,$year)) $donation['payment_amount'] *= 1.25;
							
					TO:	
						if ($donation['giftaid_status'] != 'not_applicable') {
							$day = date('d',strtotime($donation['payment_date_received']));
							$month = date('m',strtotime($donation['payment_date_received']));
							$year = date('Y',strtotime($donation['payment_date_received']));
							if($this->declarations_m->check_for_specific_donation($id, $donation['id'])) $donation['payment_amount'] *= 1.25;
							elseif($this->declarations_m->check_for_date($id,$day,$month,$year)) $donation['payment_amount'] *= 1.25;
						}

						

ADDITIONAL INFO	FOR TEST	 
		 	
	To call cron file: http://test14.toucantech.com/cron/hjk456 
	
	If you'd like to follow the process: http://test14.toucantech.com/cron/process/hjk456/1
	
	AND in index (set this true): 
			case PYRO_PRODUCTION:
			ini_set('display_errors', true);

	set cron: crontab -e on testserver
	
	HELPER - to set back changes in database
	
	Delete records from all of the affected tables

	DELETE FROM default_newsletters_lists WHERE id > 260;
	DELETE FROM default_newsletters_newsletters WHERE id > 260;
	DELETE FROM default_newsletters_newsletters_lists WHERE lists_id > 260;
	DELETE FROM default_newsletters_queue WHERE list_id > 260;
	DELETE FROM default_newsletters_subscribers_lists WHERE lists_id > 260;
	DELETE FROM default_activity_tracker WHERE id > 310;
	DELETE FROM default_activity_tracker_user_association WHERE activity_id > 310;


CHANGES:_

	//-------------------- FOR TEST: 
	
			newsletters_subscribers table :: id = 25 ->
					change: info@pelicanconnect.com e-mail to yours: lajos@toucantech.com
			default_users table: id = 1
					change: info@pelicanconnect.com e-mail to yours: lajos@toucantech.com
			default_users table: id = 123
					change: mehdi@toucantech.com e-mail to yours: lajos@toucantech.com		
					
			\network-site\addons\default\modules\network_settings\controllers\email_templates.php
		
			//Inside function index
				
				/*
					!!!!!! TEST TASK 962 - WEEKLY FRIDAY NEWSLETTER
				*/			
				require_once __DIR__ . '/../../clubs/events.php';					
				$events_obj = new Events_clubs();					
				$events_obj->send_newsletter();
				echo "Logging Weekly newsletters test is done. Don't forget, "; 
				echo "you need to change commit, news ...etc, because changes activate sending newsletter.";
				echo "<br>";
				echo "<STRONG>IMPORTANT!!!!</STRONG> BEFORE MERGE, WE NEED TO TAKE OUT SOME RESTRICTION!";
				
					
				/*
					CALL URL: base_uri/admin-portal/email-center/system-emails
					
					!!!!!! END TEST TASK 962 - WEEKLY FRIDAY NEWSLETTER
				*/	
			
	//----------------------------- END TEST						
