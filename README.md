FOR PRIVACY AND CODE PROTECTING REASONS THIS IS A SIMPLIFIED VERSION OF CHANGES AND NEW FEATURES

TASK DATE: 04.04.2017 - FINISHED: 04.04.2017

TASK LEVEL: EASY - TWO JOBS iN ONE 

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
				
GITHUB REPOSITORY CODE: hotfix/task-1464-gift-aid-not-applicable


CHANGES 

	NEW FILE: 
	
		email_banner.php

	IN FILES:
	
		events.php

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
				

		payments_m.php
		
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
