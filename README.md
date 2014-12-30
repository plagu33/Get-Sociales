<?php

//hashtag
define("HASHTAG","petertest");

//twitter
define("CONSUMER_KEY","");
define("CONSUMER_SECRET","");
define("ACCESS_TOKEN","");
define("ACCESS_TOKEN_SECRET","");

//Instagram
define('CLIENT_ID','');

define('CLIENT_SECRET','');

if(!isset($_GET["task"])) die;
$task = $_GET["task"];

switch ($task){
    case 'sociales':
        getSociales();        
    break;        
}

function getSociales(){

	/*tweets*/	
	require_once('libraries/twitteroauth/OAuth.php');
	require_once('libraries/twitteroauth/twitteroauth.php');

	$tweet   = new TwitterOAuth(CONSUMER_KEY,CONSUMER_SECRET,ACCESS_TOKEN,ACCESS_TOKEN_SECRET);
	$exclude = '-filter:retweets';//no retweets
	//$params  = array('q' =>'#'.HASHTAG.$exclude);
	//$params  = array('from' => HASHTAG);
	$results =  $tweet->get('search/tweets',$params);

	foreach ($results->statuses as $key){
		
		$image = $key->entities->media[0]->media_url;			

		//if($image!=""){

			$created_at        = $key->created_at;
			$id_str            = $key->id_str;
			$text              = $key->text;
			$source            = $key->source;
			$username          = $key->user->name;
			$screen_name       = $key->user->screen_name;
			$profile_image_url = $key->user->profile_image_url_https;

			//saveTw($screen_name,$profile_image_url,$text,$id_str,$username,$image);

		//}
		
	}	
	/*tweets*/

	/*instagram*/
	$json   = file_get_contents_curl('https://api.instagram.com/v1/tags/'.HASHTAG.'/media/recent?client_id='.CLIENT_ID);
	$array  = json_decode($json,true);
	
	foreach($array['data'] as $data){

		$video = $data['videos']['standard_resolution']['url'];

		if($video==""){

			$image             = $data['images']['standard_resolution']['url'];
			$username          = $data['user']['username'];
			$usuario           = $data['user']['full_name'];
			$texto             = utf8_decode($data['caption']['text']);
			$profile_image_url = $data['caption']['from']['profile_picture'];
		
			//saveIns($image,$username,$texto,$profile_image_url,$usuario);

		}

	}
	/*instagram*/				

}

function file_get_contents_curl($url) {
	$ch = curl_init();

	curl_setopt($ch, CURLOPT_HEADER, 0);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); // hacer curl regrese data
	curl_setopt($ch, CURLOPT_URL, $url);

	$data = curl_exec($ch);
	curl_close($ch);

	return $data;
}
